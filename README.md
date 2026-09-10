# Football CV Tracking

Football match footage → player and ball tracking, team classification, and performance metrics, built with YOLOv8 and OpenCV.

## Pipeline

1. **Ball detection** — hybrid YOLOv8 + HSV color detection with fallback logic
2. **Player detection & tracking** — YOLOv8 person detection + centroid-based cross-frame tracking with persistent IDs, confidence filtering, speed-cap filtering to reject bad matches, ghost-detection removal
3. **Team classification** — jersey color clustering (k-means) mapped to persistent player IDs
4. **Performance metrics** — per-player distance, speed, sprint count; aggregated to team-level distance

## Known limitations

- Homography (pixel-to-real-world-meters) is parked — metrics are currently in pixels/frame, not meters/km-h
- Players 7 and 8 lose some legitimate frames due to speed-cap tuning (minor, not a correctness bug)
- Tracking uses centroid-matching rather than full Re-ID — occlusion or players crossing paths can still confuse IDs; Re-ID via jersey-color matching is planned next
