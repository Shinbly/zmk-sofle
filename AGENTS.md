# Development Workflow

## CI Feedback Loop

1. **Modification** - Make code changes
2. **Push** - Commit and push to GitHub (wait for user approval before push)
3. **Wait** - Let GitHub Actions build run (~5-6 min)
4. **CI Verify** - Check build result with `gh run list` and `gh run view`

```bash
# Check build status
gh run list --limit 1

# If failed, view error
gh run view <run-id> --log-failed | grep -E "error:|Error|FAILED"
```

## OLED Display Configuration

This project uses an SH1106 OLED display (I2C).
- Compatible string: `"solomon,ssd1306"` (not sh1106)
- I2C pins: SDA=P0.26, SCL=P0.27
- I2C address: 0x3c
