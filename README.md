# Wave 1 Blocks
```mermaid
graph LR
    A([FinetuneGroot]) --- B(IsaacSim)--- C[heavy p.large]
    D([HandRemoval]) --- E(HandRemoval) --- C
    F([LeRobotConversion]) --- G(LeRobotConversion) --- H[medium]
    I([SyncTo30]) --- O(VideoEdit) --- S[small]
    K([CompositeCommand]) --- O
    X([TestBlockCommand]) --- Y(Test) --- Z[minimal]
    X1([TestAWSCommand]) --- Y
    X2([SyncS3BucketCommand]) --- Y
    X3([TestLongBlockCommand]) --- Y

    %% Legend
    subgraph Legend
        L1[Environment Created]
        L2[Pushed to ECR]
        L3[Cloud Batch]
        L4[Cloud Test]
    end


%% Define Color Schemes for Statuses
classDef environmentCreated fill:#2563eb,stroke:#1d4ed8,color:#fff
classDef pushedToECR fill:#0d9488,stroke:#0f766e,color:#fff
classDef cloudBatch fill:#d97706,stroke:#b45309,color:#fff
classDef cloudTest fill:#16a34a,stroke:#15803d,color:#fff

class Z environmentCreated
class H environmentCreated
class Y pushedToECR
class G pushedToECR
class O pushedToECR
class S environmentCreated
class X cloudTest
class X1 cloudTest
class X2 cloudTest
class X3 cloudTest
class I cloudTest
class K cloudTest
class F cloudBatch

class L1 environmentCreated
class L2 pushedToECR
class L3 cloudBatch
class L4 cloudTest
```

### This is how you build a container and push to AWS ECR.
```bash

aws ecr get-login-password --region us-east-2 | sudo docker login --username AWS --password-stdin 538091937392.dkr.ecr.us-east-2.amazonaws.com

REPO="blocks/<block_name>"
cd container
docker build -t $REPO . --no-cache
docker tag $REPO:latest 538091937392.dkr.ecr.us-east-2.amazonaws.com/$REPO:latest
docker push 538091937392.dkr.ecr.us-east-2.amazonaws.com/$REPO:latest
# To Test
sudo docker run --rm REPO:latest <command>
```