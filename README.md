# Football CV Tracking

Football match footage → player and ball tracking, team classification, and performance metrics, built with YOLOv8, ByteTrack, and OpenCV.

## Pipeline

1. **Ball detection** — hybrid YOLOv8 + HSV color detection with fallback logic
2. **Player detection & tracking** — YOLOv8 person detection with ByteTrack (Kalman-filtered) for persistent IDs, plus confidence filtering
3. **Re-identification** — jersey-color similarity merges a new ID into a recently disappeared one (distance < 15), splicing their position histories into one track
4. **Team classification** — jersey color clustering (k-means, K=2) mapped to persistent player IDs
5. **Pitch calibration** — homography maps pixels to meters (calibrated on one corner of the pitch, 0.16 m error on a validation point)
6. **Performance metrics** — per-player distance, speed, and sprint count in meters; aggregated to team-level distance

## Data cleaning

Speeds are computed only between consecutive frames, with 5-frame smoothing and a 12 m/s cap. Players with fewer than 25 speed samples are dropped.

## Known limitations

- Re-ID uses jersey color only. On a 705-frame stress test it produced 59 total IDs for a much smaller set of real players, and raising the match threshold did not reduce that count. There is no ground truth to tune against, so this is documented rather than fixed
- Calibration covers one corner only. Positions far from it extrapolate badly (about 45% of converted points fell outside the pitch and were dropped), so distances are most reliable for players who stayed near that area
- Speeds are inflated when the camera pans, since the mapping assumes a fixed camera. Many players show a steady 5-8 m/s, so treat speeds as relative, not exact
- Team classification uses average jersey color and splits roughly 2:1 instead of 11 vs 11, so referees or tracking errors are probably mixed in. Labels 0/1 can swap between runs
- The sprint rule (10+ frames above 7 m/s) is tuned to this one clip, not a standard definition
