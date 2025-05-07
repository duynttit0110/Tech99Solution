1. Check Disk Usage by Directory (Identify which directory is consuming the most space)
sudo du -h --max-depth=1 / | sort -hr | head -n 10
---
2. Check Disk Partitions (Confirm if it's the root partition (/) or a mounted volume)
df -hT
---
3. Identify Large Files (Surface abnormally large files such as logs, caches, core dumps)
sudo find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null | sort -k 5 -rh | head -n 10
---
4. Check Log Files (Large NGINX logs due to verbose logging or repeated errors)
sudo du -sh /var/log/*
sudo tail -n 100 /var/log/nginx/error.log
