# 🧪 Pantheon Evaluation - README 

## ⚙️ System Setup

```bash
# Update and upgrade system
sudo apt update && sudo apt upgrade -y

# Install build and networking dependencies
sudo apt install -y build-essential zlib1g-dev libncurses5-dev \
libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev \
wget curl git net-tools unzip cmake pkg-config
```

## 🐍 Install Python 2.7.18 (Custom Build)

```bash
cd /tmp
wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz
tar -xf Python-2.7.18.tgz
cd Python-2.7.18
./configure --prefix=/opt/python2.7
make -j$(nproc)
sudo make install
sudo ln -s /opt/python2.7/bin/python2.7 /usr/bin/python
python --version  # Confirm: Python 2.7.18
```

## 📦 Install pip2 and Python Dependencies

```bash
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
python get-pip.py
pip2 install --user pyyaml==5.1.2

# Add ~/.local/bin to PATH
echo 'export PATH=$PATH:~/.local/bin' >> ~/.bashrc
source ~/.bashrc
```

## 🌐 Install Mahimahi for Network Emulation

```bash
sudo apt install -y mahimahi
which mm-delay  # Confirm it's installed
```

## 🔁 Clone and Set Up Pantheon

```bash
cd ~
git clone https://github.com/StanfordSNR/pantheon.git
cd pantheon
git submodule update --init --recursive
sudo tools/install_deps.sh
python src/experiments/setup.py --setup --all
```

---

## 🚦 Running Tests with Pantheon

### ✅ Quick Sanity Check

```bash
python src/experiments/test.py local --schemes "cubic bbr"
```

---

## 📊 Part B Experiment 1: 50 Mbps, 10 ms

```bash
cd ~/pantheon
python src/experiments/test.py local \
    --schemes "vegas cubic bbr" \
    --run-times 1 \
    --runtime 60 \
    --data-dir results_50mbps_10ms \
    --uplink-trace traces/50mbps.trace \
    --downlink-trace traces/50mbps.trace \
    --prepend-mm-cmds "mm-delay 5"
```

### 🔍 Analyze and Generate Report

```bash
python src/analysis/analyze.py --schemes "vegas cubic bbr" --data-dir results_50mbps_10ms
python src/analysis/report.py --schemes "vegas cubic bbr" --data-dir results_50mbps_10ms
```

> Outputs (in `results_50mbps_10ms/`):
> - Throughput/Delay Graphs: `*_datalink_throughput_run1.png`, `*_datalink_delay_run1.png`
> - Summary Reports: `pantheon_summary.pdf`, `pantheon_report.pdf`

---

## 📊 Part B Experiment 2: 1 Mbps, 200 ms

```bash
python src/experiments/test.py local \
    --schemes "vegas cubic bbr" \
    --run-times 1 \
    --runtime 60 \
    --data-dir results/1mbps_200ms \
    --uplink-trace traces/50mbps.trace \
    --downlink-trace traces/50mbps.trace \
    --prepend-mm-cmds "mm-delay 100"
```

> 💡 Used same `50mbps.trace` but emulated 200 ms latency via `mm-delay 100`

### 🔍 Analyze and Generate Report

```bash
python src/analysis/analyze.py --schemes "vegas cubic bbr" --data-dir results/1mbps_200ms
python src/analysis/report.py --schemes "vegas cubic bbr" --data-dir results/1mbps_200ms
```

---

## 🧹 Cleanup (Optional)

```bash
# Delete old traces or results if needed
rm -rf traces/1mbps_200ms.trace
rm -rf results_1mbps_200ms  
```
