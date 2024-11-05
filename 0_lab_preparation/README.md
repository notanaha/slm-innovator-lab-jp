---
layout: home
title: Lab 0. Requirements (Skip if you have already set up)
nav_order: 3
---

# Lab 0. Requirements (Skip if you have already set up)

## 1. Requirements

### Checklist

{: .note }
このハンズオン ラボに参加するお客様は、Microsoft の営業担当者と話し合い、チェックリストを完了する必要があります。チェックリストは最終的に営業担当者が記入しますが、適切な状況/適切な人が関与していることを確認するために、チェックリストの完成にご協力ください。

{: .warning}
AI Document Intelligence リソースを作成するときは、**米国東部、米国西部 2、ヨーロッパ西部のいずれか**のリージョンでリソースを作成してください。そうしないと、リソースにアクセスしようとしたときに 404 エラーが発生する可能性があります。([出典](https://learn.microsoft.com/en-us/answers/questions/1514842/document-intelligence-ai-returns-404))

### 実践的な要件
- [Azure OpenAI] サービスにアクセスできることを確認します 。
- [Azure ML] ワークスペースをセットアップし  、 `<WORKSPACE_NAME>` `<RESOURCE_GROUP>` `<SUBSCRIPTION_ID>`を取得します。
- [Azure AI スタジオ]でプロジェクトを作成します 。
- LLM トレーニングには、1 つの NVIDIA A100 GPU (**[Standard_NC24ads_A100_v4]**)をお勧めします。予算が限られている場合、または専用のクォータがない場合は低優先度の VM を選択します。
- LLM サービスには、2 つの NVIDIA V100 GPU (**[Standard_NC6s_v3]**) をお勧めします。Azure ML の提供に 1 つではなく 2 つの GPU が必要な理由については、以下のメモを参照してください。

### Azure Developer CLI (azd) を使用した Azure リソースのデプロイ
最も簡単な方法をお探しの場合、このラボでは、Bicep を使用してすべてを簡単にプロビジョニングできます。次の手順では、必要な Azure リソースをプロビジョニングします。Azure [Developer CLI とはから](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/overview?tabs=windows#a-sample-azd-workflow) CLI をダウンロードします。 まだインストールしていない場合。 

Azure アカウントにログインします。

```shell
azd auth login
```

GitHub Codespaces ユーザーの場合は、前のコマンドが失敗した場合は、次のことを試してください。

```shell
 azd auth login --use-device-code
```

azd up を実行する - これにより、Azure ML ワークスペース、Azure OpenAI リソース、Application Insights、Azure Document Intelligence などの Azure リソースがプロビジョニングされます。

```shell

azd up
```
**重要**: このコマンドによって作成されるリソースには、主に AI Search リソースからすぐにコストが発生することに注意してください。これらのリソースは、コマンドが完全に実行される前にコマンドを中断した場合でも、コストが発生する可能性があります。 `azd down` リソースを手動で実行または削除して、不要な支出を回避できます。
2 つの場所を選択するように求められます。1 つは大部分のリソース用で、もう 1 つは OpenAI リソース (現在は短いリストです) 用です。この場所リストは、OpenAI モデルの可用性テーブルに基づいており、可用性が変化すると古くなる可能性があります。
アプリケーションが正常にデプロイされると、コンソールに URL が出力されます。そのURLをクリックして、ブラウザでアプリケーションを操作します。次のようになります。

### 注意
- ** サービングのためにGPUクォータの増加(\*12コア)[Standard_NC6s_v3]を要求します。** 
- プライベート環境で構成する場合は、サービスにアクセスするためのプライベートネットワークまたは VPN を設定します。
- **[低優先度 VM]** ご利用いただける状況は地域によって異なる場合があります。
- Azure ML ワークスペース内にデータとモデルを格納するために使用される任意の BLOB ストレージの接続を設定します。
- VM または GPU の必要に応じて、クォータの引き上げをリクエストします。
- Azure ML ワークスペースのネットワーク構成は、セットアップ後に変更することはできません。必要に応じて、新しいワークスペースを作成します。
- コンピューティング インスタンスが Azure ML ワークスペースと同じリージョンにあることを確認します。それ以外の場合は、VPNまたはプライベートリンクを設定します。
- Azure AI Studio コンピューティング インスタンスを使用している場合は、トレーニング ジョブを実行できないことに注意してください。

{: .note }
管理されたオンライン エンドポイントの場合、 [Azure ML では、デプロイのクォータの 20% が予約されています].[^1] デプロイ内の VM SKU に対して特定の数のインスタンスをリクエストする場合は、 `ceil(1.2 × number of instances requested for deployment) × number of cores for the VM SKU` エラーが発生しないように、使用可能なクォータが必要です。たとえば、 `Standard_NC6s_v3` デプロイで VM (6 コアが付属) の 1 つのインスタンスを要求する場合、12 コア (ceil(1.2 × 1 インスタンス) = 2、2、× 6 コア) のクォータが使用可能である必要があります。  

ワークショップ中に一般的な問題を回避するために、これらのポイントが守られていることを確認してください。

## 2. コンピュートインスタンスと設定ファイルの設定
- 1️⃣ コンピューティングリソースを準備する
- 2️⃣ リポジトリをクローンする
- 3️⃣ ファイルの作成 `.env` とAzure OpenAIおよびAzure Document Intelligenceの詳細の追加
- 4️(3)セットアップconfig.yml
-  セットアップの検証を開始する 

### 1️⃣ コンピューティングリソースを準備する
1. Azure ML Studio でコンピューティング インスタンスを作成します。Azure ML Studio >コンピューティング インスタンス>コンピューティング インスタンスに移動し、新しいコンピューティング インスタンスを作成します。
![コンピューティング インスタンスを作成する](images/create_compute.jpg)

2. コード開発には、**[Standard_E2as_v4](AMD 2コア、16GB RAM、32GBストレージ)または **[Standard_DS11_v2]** (Intel 2コア、14GB RAM、28GBストレージ、GPUなし)ボタンをクリックして`Review+Create`コンピュートインスタンスを作成することをお勧めします。

3. コンピューティング インスタンスが作成され、ステータスが [実行中] に変更されたら、 `Jupyter` または `VS Code(Desktop)` をクリックして  Jupyter ノートブックとターミナルを開きます。

![Jupyterを開く](images/open_jupyter_notebook.jpg)

### 2️⃣リポジトリをクローンし、必要なパッケージをインストールします
1. お使いの環境でターミナルに移動し、リポジトリをクローンします。 

```shell
git clone https://github.com/Azure/slm-innovator-lab.git
```


2. 次のコマンドを使用して、requirements.txtファイルにリストされているすべてのPythonモジュールとパッケージをインストールします。

```shell
cd slm-innovator-lab # Change to the directory where the repository is cloned
ENVIRONMENT=azureml_py310_sdkv2
conda activate "$ENVIRONMENT"
pip install -r requirements.txt
```

3. 複雑なPDFの処理にUnstructured toolkitを使用する場合は、必ず実行 `startup_unstructured.sh` してインスタンス起動スクリプトに含めてください。

```shell
./startup_unstructured.sh
```

### 3️⃣ ファイルの作成 `.env` とAzure OpenAIおよびAzure Document Intelligenceの詳細の追加
1. アカウントに合わせてファイルを変更することを忘れないでください `.env` 。名前を変更する `.env.sample` `.env` か、コピーして使用します。

```shell
# .env
# this is a sample for keys used in this code repo. 
# Please rename it to .env before you can use it
AZURE_OPENAI_ENDPOINT=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
AZURE_OPENAI_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# https://learn.microsoft.com/en-us/azure/ai-services/openai/api-version-deprecation
AZURE_OPENAI_API_VERSION=2024-08-01-preview
AZURE_OPENAI_DEPLOYMENT_NAME=gpt-4o-mini

AZURE_DOC_INTELLIGENCE_ENDPOINT=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
AZURE_DOC_INTELLIGENCE_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

2. ファイル内の Azure OpenAI 認証情報を照合するには `.env` 、モデル デプロイ> Azure AI Studio > Deployments に移動し、Azure Open AI モデルをデプロイした後で Azure OpenAI エンドポイントと API キーを取得してください。LLM のデプロイ プロセスについて理解したい場合は、[Azure AI Studio を使用して Azure OpenAI モデルをデプロイする方法を参照してください](https://learn.microsoft.com/en-us/azure/ai-studio/how-to/deploy-models-openai)。
![新しいフローを作成する](images/copy_api_endpoint_key.jpg)

3. このラボで PDF ファイルの読み取りと前処理を行う場合は、Azure Document Intelligence エンドポイントと API キーをファイルに追加する必要があります `.env` 。Azure Document Intelligence 認証を照合するため。Azure Document Intelligence モデルをデプロイした後、モデルのデプロイ> Azure AI services Document Intelligence に移動して、Azure Document Intelligence エンドポイントと API キーを取得してください。Document Intelligence の作成プロセスについて理解するには、[Document Intelligence の作成リソースを参照してください](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/create-document-intelligence-resource?view=doc-intel-4.0.0)。
![新しいフローを作成する](images/copy_doc_endpoint_key.jpg)



### 4️(3)セットアップconfig.yml
サブスクリプション ID、リソース グループ、ワークスペース名を取得するには、Azure ML Studio に移動してプロファイルを開きます。 `2_slm-fine-tuning-mlstudio/phi3/config.yml` Azure サブスクリプション、リソース グループ、ワークスペース、およびデータ名と一致するようにファイルを変更します。
![ML Studio の認証情報をコピーする](images/copy_ml_auth_info.jpg)

```yaml
config:
    AZURE_SUBSCRIPTION_ID: "<YOUR-SUBSCRIPTION-ID>" # Please modify to your subscription
    AZURE_RESOURCE_GROUP: "<YOUR-RESOURCE-GROUP>" # Please modify to your Azure resource group
    AZURE_WORKSPACE: "<YOUR-AZURE-WORKSPACE>" # Please modify to your Azure workspace
    AZURE_DATA_NAME: "hf-ultrachat" # Please modify to your AzureML data name
    DATA_DIR: "./dataset"
    CLOUD_DIR: "./cloud"
    HF_MODEL_NAME_OR_PATH: "microsoft/Phi-3.5-mini-instruct"
    IS_DEBUG: true
    USE_LOWPRIORITY_VM: true
    ...
```


###  セットアップの検証を開始する 
Jupyter Notebook[ を開いて続行](1_get_started.ipynb)し、表示される手順に従います。

[Azure OpenAI]: https://oai.azure.com/
[Azure ML]: https://ml.azure.com/
[Azure AI スタジオ]: https://ai.azure.com/
[Standard_DS11_v2]: https://learn.microsoft.com/azure/virtual-machines/sizes/memory-optimized/dv2-dsv2-series-memory
[Standard_E2as_v4]: https://learn.microsoft.com/en-us/azure/virtual-machines/sizes/memory-optimized/easv4-series
[Standard_NC24ads_A100_v4]: https://learn.microsoft.com/en-us/azure/virtual-machines/sizes/gpu-accelerated/nca100v4-series?tabs=sizebasic
[Standard_NC6s_v3]: https://learn.microsoft.com/azure/virtual-machines/sizes/gpu-accelerated/ncv3-series?tabs=sizebasic
[低優先度 VM]: https://learn.microsoft.com/en-us/azure/machine-learning/how-to-manage-optimize-cost?view=azureml-api-2#low-pri-vm
[Azure ML では、デプロイのクォータの 20% が予約されています]: https://learn.microsoft.com/en-us/azure/machine-learning/how-to-manage-quotas?view=azureml-api-2


[^1]: この追加のクォータは、OS のアップグレードや VM の復旧など、システムが開始する操作用に予約されており、そのような操作が実行されない限り、コストは発生しません。