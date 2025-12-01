# Gunakan Python 3.10
FROM python:3.10

# Set working directory
WORKDIR /code

# 1. Instal dependensi sistem (Wajib untuk OpenCV & InsightFace)
# PERBAIKAN: Mengganti 'libgl1-mesa-glx' menjadi 'libgl1'
RUN apt-get update && apt-get install -y \
    build-essential \
    libgl1 \
    libglib2.0-0 \
    wget \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# 2. Copy requirements dulu (agar cache layer docker efisien)
COPY requirements.txt .

# 3. Instal library Python
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# 4. Buat folder untuk cache model InsightFace dan set permission
# Hugging Face butuh folder yang bisa ditulisi oleh user (ID 1000)
RUN mkdir -p /code/.insightface/models/buffalo_l
RUN chmod -R 777 /code

# 5. Download Model InsightFace (buffalo_l) manual di sini
# Agar tidak error saat runtime
RUN wget https://github.com/deepinsight/insightface/releases/download/v0.7/buffalo_l.zip -O /tmp/buffalo_l.zip && \
    unzip /tmp/buffalo_l.zip -d /code/.insightface/models/buffalo_l && \
    rm /tmp/buffalo_l.zip

# 6. Copy seluruh kode aplikasi
COPY . .

# 7. Buat user non-root (Standar Hugging Face Spaces)
RUN useradd -m -u 1000 user
USER user
ENV HOME=/home/user \
    PATH=/home/user/.local/bin:$PATH

# 8. Jalankan Aplikasi pada Port 7860 (Standar HF)
CMD ["gunicorn", "-b", "0.0.0.0:7860", "app:app", "--workers", "1", "--threads", "8", "--timeout", "0"]