---
title: Realizar testes automatizados utilizando o Azurite
titleSuffix: Azure Storage
description: Aprenda a escrever testes automatizados contra pontos finais privados para o Azure Blob Storage utilizando a Azurite.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112046"
---
# <a name="run-automated-tests-by-using-azurite"></a>Realizar testes automatizados utilizando o Azurite

Aprenda a escrever testes automatizados contra pontos finais privados para o Azure Blob Storage utilizando o emulador de armazenamento Azurite.

## <a name="run-tests-on-your-local-machine"></a>Testes na sua máquina local

1. Instale a versão mais recente do [Python](https://www.python.org/)

1. Instalar [explorador de armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/)

1. Instalar e executar [a Azurite:](../common/storage-use-azurite.md)

   **Opção 1:** Use npm para instalar e, em seguida, executar Azurite localmente

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Opção 2:** Use Docker para executar Azurite

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. No Azure Storage Explorer, **selecione Anexar a um emulador local**

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Screenshot do Azure Storage Explorer ligando-se à fonte de armazenamento Azure.":::

1. Forneça um **nome de exibição** e número **de porta Blobs** para ligar a Azurite e utilize o Azure Storage Explorer para gerir o armazenamento de bolhas locais.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Screenshot do Azure Storage Explorer anexando-se a um emulador local.":::

1. Criar um ambiente virtual do Python

   ```bash
   python -m venv .venv
   ```

1. Crie um recipiente e inicialize variáveis ambientais. Utilize um ficheiro [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) para gerar testes. Aqui está um exemplo de um ficheiro conftest.py:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > O valor mostrado `AZURE_STORAGE_CONNECTION_STRING` é o valor padrão para Azurite, não é uma chave privada.

1. Instale dependências listadas num ficheiro [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt)

   ```bash
   pip install -r requirements.txt
   ```

1. Ensaios de execução:

   ```bash
   python -m pytest ./tests
   ```

Após a realização de testes, pode ver os ficheiros no armazenamento de bolhas Azurite utilizando o Azure Storage Explorer.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Screenshot do Azure Storage Explorer mostrando ficheiros gerados pelos testes.":::

## <a name="run-tests-on-azure-pipelines"></a>Realizar testes em Gasodutos Azure

Após a realização de testes localmente, certifique-se de que os testes passam nos [Gasodutos Azure](/azure/devops/pipelines). Utilize uma imagem Docker Azurite como agente hospedado no Azure, ou use npm para instalar a Azurite. O exemplo seguinte da Azure Pipelines utiliza npm para instalar o Azurite.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Depois de executar os testes de Gasodutos Azure, deverá ver uma saída semelhante a esta:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Screenshot dos resultados dos testes dos Gasodutos Azure.":::

## <a name="next-steps"></a>Passos seguintes

- [Utilize o emulador Azurite para o desenvolvimento local do armazenamento Azure](../common/storage-use-azurite.md)
- [Amostra: Utilização de Azurite para esboçar testes de armazenamento de bolhas no gasoduto Azure DevOps](https://github.com/Azure-Samples/automated-testing-with-azurite)
