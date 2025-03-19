 
# NVIDIA Jetson AGX Xavier에서 Ollama와 Gemma 3를 이용한 Streamlit 챗봇 구축

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![image](https://github.com/user-attachments/assets/1f6a6447-1220-464d-84da-b3cc6db6b057)
![image](https://github.com/user-attachments/assets/ff00a35d-f0f2-4fde-9c94-cf5115da8ab8)
![image](https://github.com/user-attachments/assets/81359d9f-330f-49ac-920d-741b4ae9e5ff)
![image](https://github.com/user-attachments/assets/2779ed9e-c00c-4a88-a3db-cd8af9251d21)
![image](https://github.com/user-attachments/assets/c56139f6-9aaf-4b01-b978-2c1604491430)


본 저장소는 NVIDIA Jetson AGX Xavier 보드에서 Ollama를 활용하여 로컬 대규모 언어 모델(LLM) 서버를 구축하고, Streamlit을 이용해 텍스트 및 이미지 입력을 받아 텍스트 답변을 생성하는 챗봇 애플리케이션을 제공합니다. 특히, Google의 Gemma 3 모델을 활용하여 이미지 이해 및 질의응답 기능을 구현했습니다.

## 프로젝트 배경 및 목적

최근 대규모 언어 모델(LLM)은 자연어 처리 분야에서 괄목할 만한 성과를 거두고 있습니다. 그러나 대부분의 LLM 서비스는 클라우드 기반으로 동작하여 인터넷 연결, 개인 정보 보호, 응답 속도 등의 측면에서 제약이 있을 수 있습니다. 본 프로젝트는 이러한 제약을 극복하고, 엣지 디바이스(Jetson AGX Xavier)에서 로컬 LLM 서버를 구축하여 다음과 같은 이점을 얻는 것을 목표로 합니다.

-   **오프라인 사용:** 인터넷 연결 없이도 LLM 사용 가능
-   **개인 정보 보호:** 데이터를 로컬에서 처리하여 개인 정보 유출 위험 감소
-   **낮은 지연 시간:** 네트워크 지연 없이 빠른 응답 속도 제공
-   **엣지 컴퓨팅 활용:** Jetson AGX Xavier의 GPU 가속을 활용한 효율적인 LLM 추론

## 시스템 아키텍처

본 프로젝트의 시스템 아키텍처는 다음과 같이 구성됩니다.

1.  **Ollama 서버 (Jetson AGX Xavier):**
    -   NVIDIA Jetson AGX Xavier 보드에 Ollama를 설치합니다.
    -   Ollama를 통해 Google Gemma 3 모델을 다운로드하고 실행합니다.
    -   Ollama는 REST API를 통해 외부(Streamlit 앱)의 요청을 처리합니다.
    -   `OLLAMA_HOST` 환경 변수를 `0.0.0.0`으로 설정하여 외부 접근을 허용합니다.
    -   방화벽(ufw)에서 Ollama API 포트(11434)를 개방합니다.

2.  **Streamlit 챗봇 (로컬 PC 또는 Jetson):**
    -   Streamlit을 사용하여 챗봇 웹 애플리케이션을 개발합니다.
    -   `requests` 라이브러리를 사용하여 Jetson의 Ollama API에 HTTP 요청을 보냅니다.
    -   사용자는 텍스트 또는 이미지를 입력하고, 챗봇은 LLM(Gemma 3)의 응답을 텍스트로 표시합니다.
    -   이미지 입력의 경우, `PIL` (Pillow) 라이브러리를 사용하여 이미지를 처리하고, `base64` 인코딩을 통해 Ollama API로 전송합니다.

## 주요 기능

-   **텍스트 질의응답:** 사용자는 텍스트로 질문을 입력하고, LLM은 질문에 대한 답변을 텍스트로 제공합니다.
-   **이미지 질의응답:** 사용자는 이미지를 업로드하고, 이미지와 관련된 질문을 텍스트로 입력하면, LLM은 이미지 내용을 이해하고 질문에 답변합니다.
-   **Ollama API 연동:** `requests` 라이브러리를 사용하여 Jetson에서 실행 중인 Ollama 서버의 API와 통신합니다.
-   **Streamlit UI:** Streamlit을 사용하여 간편하고 직관적인 사용자 인터페이스를 제공합니다.

## 사용된 기술 및 라이브러리

-   **Ollama:** 로컬 LLM 실행 및 관리를 위한 도구 ([https://ollama.ai/](https://ollama.ai/))
-   **Gemma 3:** Google에서 개발한 개방형 대규모 언어 모델 ([https://ai.google.dev/gemma/](https://ai.google.dev/gemma/))
-   **Streamlit:** Python 기반의 인터랙티브 웹 애플리케이션 프레임워크 ([https://streamlit.io/](https://streamlit.io/))
-   **requests:** Python HTTP 클라이언트 라이브러리
-   **PIL (Pillow):** Python 이미지 처리 라이브러리
-   **base64:** 이미지 데이터를 Base64 형식으로 인코딩/디코딩하기 위한 라이브러리
-   **tempfile:** 임시 파일 생성을 위한 라이브러리

## 설치 및 실행 방법

### 1. Ollama 서버 설정 (Jetson AGX Xavier)

1.  **Jetson AGX Xavier 준비:**
    -   JetPack SDK 설치 및 설정
    -   충분한 저장 공간 확보 (SSD 권장)
    -   인터넷 연결 확인

2.  **Ollama 설치:**

    ```bash
    curl https://ollama.ai/install.sh | sh
    ```

3.  **Gemma 3 모델 다운로드:**

    ```bash
    ollama pull gemma3:4b
    ```

4.  **Ollama 서버 실행 (외부 접근 허용):**

    - 방법 1 (권장): `systemctl` 사용
     ```bash
      sudo systemctl edit ollama.service
     ```
      다음 내용을 추가:
       ```
      [Service]
      Environment="OLLAMA_HOST=0.0.0.0"
       ```
     저장 후,
       ```
      sudo systemctl daemon-reload
      sudo systemctl restart ollama
       ```

    - 방법 2: 환경 변수 직접 설정 (임시)
        ```bash
        export OLLAMA_HOST=0.0.0.0
        ollama serve
        ```

5.  **방화벽 설정 (ufw):**

    ```bash
    sudo ufw allow 11434/tcp
    ```

### 2. Streamlit 챗봇 실행 (로컬 PC 또는 Jetson)

1.  **저장소 클론:**

    ```bash
    git clone https://github.com/hwkims/nvidia-jetson-xavier-agx-ollama-gemm3.git
    cd nvidia-jetson-xavier-agx-ollama-gemm3
    ```

2.  **Python 가상 환경 생성 및 활성화 (선택 사항이지만 권장):**

    ```bash
    python3 -m venv venv
    source venv/bin/activate  # Linux/macOS
    venv\Scripts\activate  # Windows
    ```

3.  **필요한 라이브러리 설치:**

    ```bash
    pip install -r requirements.txt
    ```

4.  **`app.py` 파일에서 `OLLAMA_HOST` 변수를 Jetson 보드의 IP 주소와 포트로 변경합니다.** (기본값: `192.168.0.5:11434`)

5.  **Streamlit 앱 실행:**

    ```bash
    streamlit run app.py
    ```

6.  웹 브라우저에서 Streamlit 앱에 접속합니다. (기본 주소: `http://localhost:8501`)

## 파일 구조
```
nvidia-jetson-xavier-agx-ollama-gemm3/
├── app.py          # Streamlit 앱 코드
├── requirements.txt  # Python 패키지 목록
└── README.md       # 프로젝트 설명
```
## 코드 설명 (`app.py`)

```python
import streamlit as st
import requests
import json
import base64
import io
from PIL import Image
import os
import tempfile

# Ollama API 엔드포인트 및 모델 설정
OLLAMA_HOST = "192.168.0.5:11434"  # Jetson IP 주소와 포트
OLLAMA_MODEL = "gemma3:4b"  # 사용할 Ollama 모델 이름

# Ollama API 호출 함수
def ollama_api(prompt, image_path=None):
    """
    Ollama API에 요청을 보내고 응답을 반환합니다.

    Args:
        prompt (str): 사용자 입력 텍스트 (질문).
        image_path (str, optional): 이미지 파일 경로. Defaults to None.

    Returns:
        str: Ollama 모델의 응답 텍스트.
             오류 발생 시 None을 반환하고 에러 메시지를 출력합니다.
    """
    data = {"prompt": prompt, "model": OLLAMA_MODEL, "stream": False, "format":"json"}

    if image_path:
        # 이미지 파일을 base64로 인코딩
        try:
            with open(image_path, "rb") as image_file:
                encoded_string = base64.b64encode(image_file.read()).decode()
            data["images"] = [encoded_string]
        except FileNotFoundError:
            st.error(f"이미지 파일을 찾을 수 없습니다: {image_path}")
            return None
        except Exception as e:
            st.error(f"이미지 처리 중 오류 발생: {e}")
            return None

    try:
        response = requests.post(f"http://{OLLAMA_HOST}/api/generate", json=data)
        response.raise_for_status()  # 200 OK가 아니면 예외 발생
        return response.json()["response"]
    except requests.exceptions.RequestException as e:
        st.error(f"Ollama API 호출 오류: {e}")
        return None
    except json.JSONDecodeError:
        st.error("Ollama API 응답이 JSON 형식이 아닙니다.")
        return None


# Streamlit 앱
st.title("Ollama 챗봇 (with Image Support)")

# 사용자 입력 (텍스트 또는 이미지)
input_type = st.radio("입력 유형 선택:", ("텍스트", "이미지"))

if input_type == "텍스트":
    user_input = st.text_input("질문을 입력하세요:")
    if st.button("질문하기"):
        if user_input:
            with st.spinner("답변 생성 중..."):
                response = ollama_api(user_input)
            if response:
                st.write("답변:")
                st.write(response)
        else:
            st.warning("질문을 입력해주세요.")

elif input_type == "이미지":
    uploaded_file = st.file_uploader("이미지를 업로드하세요:", type=["jpg", "jpeg", "png"])
    if uploaded_file is not None:
        image = Image.open(uploaded_file)
        st.image(image, caption="업로드된 이미지", use_column_width=True)

        # 임시 파일로 저장 (경로 문제 해결)
        with tempfile.NamedTemporaryFile(delete=False, suffix=".jpg") as tmp_file:
            image.save(tmp_file.name)
            temp_image_path = tmp_file.name

        user_question = st.text_input("이미지에 대해 질문을 입력하세요 (선택 사항):")
        if st.button("질문하기"):
            with st.spinner("답변 생성 중..."):
                response = ollama_api(user_question, temp_image_path)
            if response:
                st.write("답변:")
                st.write(response)

            # 임시 파일 삭제
            os.remove(temp_image_path)
```

-   **`OLLAMA_HOST`**:  Jetson 보드에서 실행 중인 Ollama 서버의 IP 주소와 포트 번호입니다.  Jetson 보드의 실제 IP 주소로 수정해야 합니다.
-   **`OLLAMA_MODEL`**:  사용할 Ollama 모델의 이름입니다.  Jetson 보드에 설치된 모델 이름으로 수정해야 합니다.
- **`ollama_api(prompt, image_path=None)` 함수:**
    -   `prompt`: 사용자가 입력한 텍스트 질문입니다.
    -   `image_path`: 사용자가 업로드한 이미지 파일의 경로입니다. (선택 사항)
    -   `data`: Ollama API에 전송할 데이터를 담는 딕셔너리입니다.
        -   `prompt`: 사용자 질문
        -   `model`: 사용할 모델
        -   `stream`: 스트리밍 사용 여부 (여기서는 `False`로 설정하여 한 번에 전체 응답을 받습니다.)
        -  `format`: 응답 형식을 json으로 지정합니다.
        -   `images`: (이미지가 있는 경우) base64로 인코딩된 이미지 데이터
    -   이미지 파일이 제공되면, `open()` 함수를 사용하여 이미지를 열고, `base64.b64encode()`를 사용하여 base64 문자열로 인코딩합니다. 인코딩된 문자열은 `data["images"]`에 추가됩니다.
    -   `requests.post()`를 사용하여 Ollama API에 POST 요청을 보냅니다.
    -   `response.raise_for_status()`: 응답 상태 코드가 200 OK가 아니면 예외를 발생시킵니다.
    -   `response.json()["response"]`: 응답 JSON에서 `"response"` 키의 값을 추출하여 반환합니다.
    -   오류 처리: 파일이 없거나, 이미지 처리 중 오류, API 호출 오류, JSON 파싱 오류가 발생하면 에러 메시지를 표시하고 `None`을 반환합니다.
-   **Streamlit UI:**
    -   `st.title()`: 앱 제목을 설정합니다.
    -   `st.radio()`: 사용자가 텍스트 또는 이미지 중 입력 유형을 선택할 수 있도록 라디오 버튼을 만듭니다.
    -   **텍스트 입력:**
        -   `st.text_input()`: 사용자로부터 질문을 입력받습니다.
        -   `st.button()`: "질문하기" 버튼을 만듭니다.
        -   `st.spinner()`: 답변 생성 중에 "답변 생성 중..." 메시지와 함께 스피너를 표시합니다.
        -   `st.write()`: 답변을 표시합니다.
    -   **이미지 입력:**
        -   `st.file_uploader()`: 사용자가 이미지를 업로드할 수 있도록 파일 업로더를 만듭니다.
        -   `st.image()`: 업로드된 이미지를 표시합니다.
        -   `tempfile.NamedTemporaryFile()`: 업로드된 이미지를 임시 파일로 저장합니다. 이렇게 하면 파일 경로 문제를 방지하고, 사용 후 파일을 안전하게 삭제할 수 있습니다.
        -   `st.text_input()`: 이미지에 대한 추가 질문을 입력받습니다(선택 사항).
        -   `os.remove()`: 임시 파일을 삭제합니다.

## 문제 해결 (Troubleshooting)

-   **`requests.exceptions.ConnectionError`:** Ollama 서버에 연결할 수 없을 때 발생합니다.
    -   Jetson에서 `ollama serve`가 실행 중인지 확인합니다.
    -   Jetson과 Streamlit 앱을 실행하는 장치가 같은 네트워크에 있는지 확인합니다.
    -   `OLLAMA_HOST` 변수에 Jetson의 올바른 IP 주소와 포트가 설정되어 있는지 확인합니다.
    -   Jetson의 방화벽(ufw)에서 11434 포트가 허용되어 있는지 확인합니다. (`sudo ufw allow 11434/tcp`)
    -   Jetson에서 `OLLAMA_HOST` 환경 변수가 `0.0.0.0`으로 설정되어 있는지 확인합니다.
-   **`curl: command not found`:** `curl` 명령어가 설치되어 있지 않을 때 발생합니다. `sudo apt-get install curl`로 `curl`을 설치합니다.
-   **`ssh: connect to host ... port 22: Connection timed out`:** SSH 접속이 안 될 때 발생합니다.
    -   Jetson에서 SSH 서버가 실행 중인지 확인합니다. (`sudo systemctl start ssh`)
    -   Jetson 방화벽에서 22번 포트가 허용되어 있는지 확인합니다. (`sudo ufw allow 22/tcp`)
-   **`ufw` 명령어 오류:**
    - `ufw`가 설치되어 있는지 확인합니다. (`sudo apt install ufw`)
     - `ufw`가 활성화되어 있는지 확인합니다. (`sudo ufw enable`)

## 기여 (Contributing)

버그를 발견하거나 개선 제안이 있으면 Issues 또는 Pull Requests를 통해 기여할 수 있습니다.

## 라이선스 (License)

이 프로젝트는 MIT License를 따릅니다. 자세한 내용은 `LICENSE` 파일을 참조하세요.
 
 
