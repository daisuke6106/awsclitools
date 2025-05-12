# gitlabをAWSECSにデプロイ

gitlabをAWSECSにデプロイ

## ECRにgitlabを登録

「```bash
# 設定変数
export AWSACCOUNT=503561457222
export REGION=ap-northeast-1
export LOCAL_REPOSITORYNAME=gitlab/gitlab-ce
export ECR_REPOSITORYNAME=gitlab-ce
# ECRリポジトリの作成
aws ecr --profile ${AWSACCOUNT}_AdministratorAccess --region ${REGION} create-repository --repository-name ${ECR_REPOSITORYNAME}
# DockerイメージをECRにプッシュ
# ECRにプッシュする前に、ECRのログイン認証を行う
aws ecr --profile ${AWSACCOUNT}_AdministratorAccess --region ${REGION} get-login-password | docker login --username AWS --password-stdin ${AWSACCOUNT}.dkr.ecr.${REGION}.amazonaws.com
# タグを付けてイメージをプッシュ
docker tag ${LOCAL_REPOSITORYNAME}:latest ${AWSACCOUNT}.dkr.ecr.${REGION}.amazonaws.com/${ECR_REPOSITORYNAME}:latest
docker push ${AWSACCOUNT}.dkr.ecr.${REGION}.amazonaws.com/${ECR_REPOSITORYNAME}:latest
```」

## ECSタスク定義

「```bash
# 設定変数
TASK_FAMILY="gitlab-ce-task-family"
CONTAINER_NAME="gitlab-ce-container"
IMAGE_URI="${AWSACCOUNT}.dkr.ecr.${REGION}.amazonaws.com/${ECR_REPOSITORYNAME}:latest"
EXECUTION_ROLE_ARN="arn:aws:iam::${AWSACCOUNT}:role/ecsTaskExecutionRole"
MEMORY="512"        # MiB
CPU="256"           # CPU単位
PORT="80"           # 公開ポート

```」

