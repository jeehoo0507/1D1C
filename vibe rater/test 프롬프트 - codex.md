로컬 Ubuntu 환경에서 Docker로 localhost 3060에서 실행되는 간단한 이미지 검색 웹사이트를 구현해줘.

목적:
img 폴더에 있는 약 300장의 동일한 고양이 사진을 자연어로 검색하는 zero-shot baseline을 테스트한다.

기술:
- Python 3.11
- PyTorch CUDA
- transformers
- google/siglip-base-patch16-224
- Helsinki-NLP/opus-mt-ko-en
- Gradio
- Docker Compose
- NVIDIA Container Toolkit

파이프라인:

이미지:
img/*
→ SigLIP image encoder
→ L2 normalized embedding
→ data/embeddings/image_embeddings.pt에 저장

검색:
한국어 query
→ 영어 번역
→ SigLIP text encoder
→ L2 normalize
→ 모든 image embedding과 matrix multiplication
→ cosine similarity Top-K
→ Gradio Gallery에 이미지와 score 표시

프로젝트 구조:

shuding-search/
├── app.py
├── build_index.py
├── search_engine.py
├── translator.py
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
└── data/
    ├── images/
    └── embeddings/

요구사항:
- gpu 사용
- 이미지 embedding은 매 검색마다 계산하지 않고 build_index.py로 사전 계산
- JPG, JPEG, PNG, WEBP 지원
- 약 300장이므로 FAISS나 vector DB 사용 금지
- cosine similarity는 normalized tensor의 matrix multiplication으로 구현
- torch.inference_mode() 사용
- Gradio 웹사이트는 localhost:7860에서 접근
- 검색창, Top-K 선택(5/10/20), 검색 버튼, 번역된 영어 query 표시, 결과 이미지 Gallery, 각 이미지 similarity score 표시
- docker compose up --build만으로 실행 가능하게 만들 것
- compose에서 GPU 사용 설정
- README에 NVIDIA Container Toolkit이 이미 설치되어 있다는 전제로 실행 방법 작성
- 모델은 Hugging Face cache를 Docker volume으로 보존해서 컨테이너 재시작마다 다시 다운로드하지 않도록 구성
- 코드에 주석 작성하지 말 것

build_index.py와 app.py가 실제로 바로 실행 가능한 완성 상태로 만들어라.
복잡한 abstraction이나 불필요한 architecture는 만들지 말고 테스트용 MVP에 집중해라.