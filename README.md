# 자율 AI 에이전트 기반 SEM 이미지 깊이 추정 시스템

이 프로젝트는 Dacon의 'SEM 이미지 깊이 추정 AI 경진대회'를 위해 개발된 자율 다중 에이전트 시스템입니다. 데이터 분석, 모델 아키텍처 탐색, 하이퍼파라미터 최적화, 최종 학습 및 추론에 이르는 전체 딥러닝 워크플로우를 여러 AI 에이전트들의 협업을 통해 자동화하는 것을 목표로 합니다.

## 주요 특징

- **자율 다중 에이전트 아키텍처**: 각기 다른 전문 분야를 가진 AI 에이전트들이 협력하여 문제를 해결합니다.
  - `DataAnalysisAgent`: 데이터셋을 분석하고 최적의 증강(Augmentation) 전략을 수립합니다.
  - `ArchitectureSearchAgent`: U-Net, CAE 등 유망한 모델 아키텍처 후보군을 제안합니다.
  - `ExperimentManagerAgent`: 주어진 설정에 따라 PyTorch Lightning으로 모델 학습 및 평가를 수행합니다.
  - `EvaluationAgent`: 예비 실험 결과를 평가하여 가장 성능이 좋은 모델을 선정합니다.
  - `OptimizationAgent`: Optuna를 사용하여 선정된 모델의 하이퍼파라미터를 자동으로 튜닝합니다.
- **자동화된 워크플로우**: 단 하나의 설정값(`MODE_TO_RUN`) 변경만으로 전체 프로세스를 제어할 수 있습니다.
  - **모델 선택 모드 (`select_model`)**: 여러 모델 아키텍처를 빠르게 비교하여 최적의 구조를 찾습니다.
  - **하이퍼파라미터 최적화 모드 (`optimize`)**: 최적의 모델 구조에 대해 가장 효율적인 하이퍼파라미터 조합을 탐색합니다.
- **고성능 딥러닝 모델**:
  - Attention 메커니즘이 적용된 **Advanced U-Net**
  - 기본적인 **Convolutional Autoencoder (CAE)**
- **체계적인 실험 관리**: PyTorch Lightning과 TensorBoard Logger를 사용하여 학습 과정을 효율적으로 관리하고 모니터링합니다.

## 기술 스택

- **Main Frameworks**: PyTorch, PyTorch Lightning
- **Hyperparameter Tuning**: Optuna
- **Core Libraries**: NumPy, PIL (Pillow), glob
- **Utilities**: tqdm, Matplotlib

## 실행 방법

노트북은 순차적으로 실행되도록 설계되었습니다.

1.  **초기 설정 (셀 1-5)**
    - 필요한 라이브러리를 설치하고 임포트합니다.
    - `open.zip` 데이터셋의 압축을 해제합니다.

2.  **핵심 컴포넌트 정의 (셀 6-11)**
    - `TransformedDataset`: 데이터 로딩 및 변환을 위한 Pytorch `Dataset` 클래스.
    - `AdvancedUNet`, `CAELightningModule`: 딥러닝 모델 아키텍처 정의.
    - `BaseAgent` 및 하위 에이전트들: 자율 시스템의 핵심 두뇌 역할을 하는 에이전트 클래스 정의.

3.  **자율 시스템 실행 (셀 13)**
    - 코드 하단의 `MODE_TO_RUN` 변수 값을 원하는 작업에 맞게 변경하여 실행합니다.
      - `MODE_TO_RUN = 'select_model'`: 여러 모델 후보군 중 최적의 아키텍처를 자동으로 선정하고, `best_model_info.json` 파일에 저장합니다.
      - `MODE_TO_RUN = 'optimize'`: `select_model` 단계에서 선정된 모델을 기반으로 Optuna를 사용해 최적의 하이퍼파라미터를 탐색하고, `best_hyperparams.json`에 저장합니다.

4.  **최종 모델 학습 및 추론 (별도 실행 필요)**
    - `optimize` 모드까지 완료한 후, 찾은 최적의 아키텍처와 하이퍼파라미터를 사용하여 모델을 끝까지 학습시킵니다.

5.  **결과 시각화 및 제출 (셀 14)**
    - 학습된 최종 모델(`best-model-....ckpt`)을 로드합니다.
    - 테스트 데이터셋에 대한 예측을 수행하고, 결과를 `visualization_results.zip` 파일로 압축하여 제출 파일을 생성합니다.
