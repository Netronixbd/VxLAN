# VXLAN Markdown Update · GitHub Upload Guide (Bangla)

এই প্যাকেজে পুরোনো ১০টি `.cfg` ফাইলের পরিবর্তে GitHub-এ সুন্দরভাবে দেখা যায় এমন `.md` ফাইল আছে। প্রতিটির command মূল ফাইল থেকে অপরিবর্তিত রাখা হয়েছে।

## Windows / Git Bash দিয়ে বিদ্যমান GitHub repo আপডেট

1. Git Bash-এ তোমার local repository-তে গিয়ে **আগে** `git pull --rebase origin main` চালাও।
2. ZIP extract করো। Extract করা `VXLAN-Huawei-Cisco` folder-এর **ভেতরের সবকিছু** তোমার local repo `F:\VxLAN\VXLAN-Huawei-Cisco` folder-এ copy/merge করো।
3. Local repo-তে আগে থেকে থাকা `configs/static/*.cfg` এবং `configs/evpn/*.cfg` ফাইলগুলো delete করো।
4. Git Bash-এ বাকি command চালাও:

```bash
cd /f/VxLAN/VXLAN-Huawei-Cisco
git add -A
git status
git commit -m "Convert VXLAN .cfg configs to professional Markdown"
git push origin main
```

> যদি অন্য PC থেকে একই repo-তে নতুন commit করে থাকো, আগে `git pull --rebase origin main` করো, তারপর extracted files copy করো। কোনো conflict হলে force push করো না।

## Upload ঠিক হয়েছে কিনা দেখবে

- https://github.com/Netronixbd/VxLAN
- https://github.com/Netronixbd/VxLAN/tree/main/configs
- `configs/static` এবং `configs/evpn`-এ সব `.md` file আছে কিনা দেখো।
- README link দিয়ে বিভিন্ন guide খোলে কিনা দেখো।

**Important:** `.md` format হচ্ছে documentation; GitHub এগুলো device-এ automatic configure করবে না। Switch-এ apply করার আগে platform command ও sample IP validate করবে।
