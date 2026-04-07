# Install / update all skills globally

```bash
for skill in skills/*/; do
  name=$(basename "$skill")
  cp -rf "$skill" ~/.claude/skills/"$name"
  echo "✓ Installed $name"
done
```
