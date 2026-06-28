# GitOps

Argo CD는 `k8s/` 경로에 있는 쿠버네티스 매니페스트를 감시하며 `acer-aio` kubeadm 클러스터를 Git 저장소와 동기화 상태로 유지합니다.

클러스터에 접근할 수 있고 Argo CD가 설치된 머신에서 다음 명령을 한 번 실행하여 배포를 시작합니다:

```bash
kubectl apply -f gitops/argocd/web-service-application.yaml
```

일반적인 배포 흐름:

1. 애플리케이션 코드를 GitLab `main` 브랜치에 푸시(Push)합니다.
2. GitLab CI가 코드를 빌드하고 테스트(SonarQube)를 마칩니다. (추후 Harbor 설정이 완료되면 이미지도 푸시됩니다).
3. 파이프라인이 GitHub의 `k8s/kustomization.yaml` 내 이미지 태그를 새로 빌드된 커밋 SHA 태그로 업데이트하고 매니페스트 변경 사항을 저장소에 자동으로 푸시합니다.
4. Argo CD가 Git의 변경 사항을 감지하고 `web-service` 네임스페이스를 자동으로 동기화(배포)합니다.

이 서비스는 Nginx NodePort `30080`을 통해 외부에 노출되며, 이는 `acer-aio` OpenStack 보안 그룹의 NodePort 범위와 매칭됩니다.

만약 CI/CD 파이프라인을 사용할 수 없는 장애 상황이라면, Docker 및 레지스트리 접근 권한, 그리고 `/home/ubuntu/.kube/acer-kubeadm.yaml` 경로의 kubectl 권한을 가진 셸에서 `scripts/web-service-emergency.rc` 스크립트를 사용하여 수동 배포할 수 있습니다.
