# Football CV Tracking

Football match footage → player and ball tracking, team classification, and performance metrics, built with YOLOv8, ByteTrack, and OpenCV.

## Pipeline

1. **Ball detection** — hybrid YOLOv8 + HSV color detection with fallback logic
2. **Player detection & tracking** — YOLOv8 person detection with ByteTrack (Kalman-filtered) for persistent IDs, plus confidence filtering
3. **Re-identification** — jersey-color similarity merges a new ID into a recently disappeared one (distance < 15), splicing their position histories into one track
4. **Team classification** — jersey color clustering (k-means, K=2) mapped to persistent player IDs
5. **Performance metrics** — per-player distance, speed, and sprint count; aggregated to team-level distance

## Known limitations

- Homography (pixel-to-real-world-meters) is not implemented — metrics are in pixels and pixels/second, not meters or km/h
- Re-ID uses jersey color only. On a 705-frame stress test it produced 59 total IDs for a much smaller set of real players, and raising the match threshold did not reduce that count. There is no ground truth to tune against, so this is documented rather than fixed
- Merging across a long occlusion gap can create a fake speed spike at the seam; this is not yet filtered
