FROM n8nio/runners:2.2.3

USER root

# 1. 複製 requirements.txt 到容器
COPY requirements.txt .

# 2. 安裝套件
RUN pip install --no-cache-dir \
    --target=/opt/runners/task-runner-python/.venv/lib/python3.13/site-packages \
    -r requirements.txt

COPY n8n-task-runners.json /etc/n8n-task-runners.json
