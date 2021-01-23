---
title: Serviço de construtores de imagem Azure de resolução de problemas
description: Resolver problemas e erros comuns ao utilizar o Azure VM Image Builder Service
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 73984694d764234e9e1ec11e6b189a9ad85d97a8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737409"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Serviço de construtores de imagem Azure de resolução de problemas

Este artigo ajuda-o a resolver problemas e a resolver problemas comuns que poderá encontrar ao utilizar o Azure Image Builder Service.

Falhas no AIB podem ocorrer em 2 áreas:
- Submissão do modelo de imagem
- Construção de imagem

## <a name="troubleshoot-image-template-submission-errors"></a>Erros de submissão do modelo de imagem de resolução de problemas

Os erros de submissão do modelo de imagem são devolvidos apenas na submissão. Não existe um registo de erro para erros de submissão do modelo de imagem. Se houve um erro durante a submissão, pode retornar o erro verificando o estado do modelo, especificamente revendo o `ProvisioningState` e `ProvisioningErrorMessage` / `provisioningError` . .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> Para o PowerShell, terá de instalar os [Módulos PowerShell do Construtor de Imagem Azure](../windows/image-builder-powershell.md#prerequisites).

As seguintes secções incluem orientação de resolução de problemas para erros comuns de submissão do modelo de imagem.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>Atualização/atualização dos modelos de imagem não está suportada atualmente

#### <a name="error"></a>Erro

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Causa

O modelo já existe.

#### <a name="solution"></a>Solução

Se submeter um modelo de configuração de imagem e a submissão falhar, um artefacto de modelo falhado ainda existe. Elimine o modelo falhado.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>A operação de recursos concluída com o estado de provisionamento terminal 'Falhado'

#### <a name="error"></a>Erro

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Causa

Na maioria dos casos, o erro de falha de implementação de recursos ocorre devido a permissões em falta.

#### <a name="solution"></a>Solução

Dependendo do seu cenário, o Azure Image Builder poderá necessitar de permissões para:
- Imagem de origem ou grupo de recursos da Galeria de Imagens Partilhadas
- Imagem de distribuição ou recurso da Galeria de Imagens Partilhadas
- A conta de armazenamento, o recipiente ou a bolha a que o personalizador de ficheiros está a aceder. 

Para obter mais informações sobre permissões de configuração, consulte [permissões do Serviço de Construtores de Imagem Configure Azure usando permissões do Serviço de Construtores de Imagem Azure ou](image-builder-permissions-cli.md) [Configure Azure Image Builder](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>Erro obtendo imagem gerida

#### <a name="error"></a>Erro

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Causa

Falta de permissões.

#### <a name="solution"></a>Solução

Dependendo do seu cenário, o Azure Image Builder poderá necessitar de permissões para:
* Imagem de origem ou grupo de recursos da Galeria de Imagens Partilhadas
* Imagem de distribuição ou recurso da Galeria de Imagens Partilhadas
* A conta de armazenamento, o recipiente ou a bolha a que o personalizador de ficheiros está a aceder. 

Para obter mais informações sobre permissões de configuração, consulte [permissões do Serviço de Construtores de Imagem Configure Azure usando permissões do Serviço de Construtores de Imagem Azure ou](image-builder-permissions-cli.md) [Configure Azure Image Builder](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>Passo de construção falhou para a versão de imagem

#### <a name="error"></a>Erro
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Causa

O Azure Image Builder não consegue localizar a imagem de origem.

#### <a name="solution"></a>Solução

Certifique-se de que a imagem de origem está correta e existe na localização do Serviço de Construtor de Imagem Azure.

### <a name="downloading-external-file-to-local-file"></a>Descarregar ficheiro externo para ficheiro local

#### <a name="error"></a>Erro

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Causa

O nome ou localização do ficheiro está incorreto ou a localização não é alcançável.

#### <a name="solution"></a>Solução

Certifique-se de que o ficheiro está acessível. Verifique se o nome e a localização estão corretos.

## <a name="troubleshoot-build-failures"></a>Falhas na construção de problemas

Para falhas na construção de imagens, pode obter o erro do `lastrunstatus` , e depois rever os detalhes na personalização.log.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Registo de personalização

Quando a construção da imagem está em execução, os registos são criados e armazenados numa conta de armazenamento. O Azure Image Builder cria a conta de armazenamento no grupo de recursos temporários quando cria um artefacto de modelo de imagem.

O nome da conta de armazenamento usa o seguinte padrão: **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Por exemplo, *IT_aibmdi_helloImageTemplateLinux01.*

Pode ver a personalização.log na conta de armazenamento no grupo de recursos, selecionando **As**  >  **Bolhas de** Conta de Armazenamento  >  `packerlogs` .  Em seguida, selecione **diretório > personalização.log**.


### <a name="understanding-the-customization-log"></a>Compreender o registo de personalização

O tronco é verboso. Cobre a construção de imagens, incluindo quaisquer problemas com a distribuição de imagem, como a replicação da Galeria de Imagens Partilhadas. Estes erros são apresentados na mensagem de erro do estado do modelo de imagem.

A .log personalização inclui as seguintes fases:

1. Implemente o VM de construção e as dependências utilizando modelos ARM para a fase de grupos de IT_ de estágio de recursos. Este palco inclui vários POSTs para o fornecedor de recursos Azure Image Builder:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Estado da fase de implantação. Esta fase inclui o estado de cada utilização de recursos:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Ligue-se ao estágio VM de construção.

    Se o Windows, o Serviço de Construtores de Imagem Azure ligar-se através do WinRM:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Se o Linux, o Serviço de Construtores de Imagem Azure ligar-se-á através do SSH:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Executar o estágio de personalização. Quando as personalizações são executadas, pode identificá-las revendo a personalização.log. Procurar *(telemetria)*.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Fase de desavisionamento. O Azure Image Builder adiciona um personalizador oculto. Esta etapa de desavisionamento é responsável pela preparação do VM para a desavisionamento. Executa o Windows Sysprep (utilizando c:\DeprovisioningScript.ps1), ou na deprovisionamento waagent linux (utilizando /tmp/DeprovisioningScript.sh). 

    Por exemplo:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Limpar o estágio. Uma vez concluída a construção, os recursos do Azure Image Builder são eliminados.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Dicas para resolução de problemas script/personalização inline
- Teste o código antes de o fornecer ao Image Builder
- Certifique-se de que a Azure Policy's e firewalls permitem a conectividade com os recursos remotos.
- Comentários de saída para a consola, como usar `Write-Host` `echo` ou, isto permitir-lhe-á pesquisar a personalização.log.

## <a name="troubleshoot-common-build-errors"></a>Resolução de problemas erros de construção comuns

### <a name="packer-build-command-failure"></a>Packer constrói falha de comando

#### <a name="error"></a>Erro

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Causa

Falha de personalização.

#### <a name="solution"></a>Solução

Revisão do registo para localizar falhas de personalizadores. Procurar *(telemetria)*. 

Por exemplo:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Tempo limite ultrapassado

#### <a name="error"></a>Erro

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Causa

A construção excedeu o tempo limite de construção. Este erro é visto no 'lastrunstatus'.

#### <a name="solution"></a>Solução

1. Reveja a personalização.log. Identifique o último personalizador a correr. Procure `(telemetry)` a partir do fundo do tronco. 

2. Verifique as personalizações do script. As personalizações podem não estar a suprimir a interação do utilizador para comandos, tais como `quiet` opções. Por exemplo, `apt-get install -y` resulta na execução do script à espera da interação do utilizador.

3. Se estiver a utilizar o `File` personalizador para descarregar artefactos superiores a 20 MB, consulte a secção soluções alternativas.

4. Reveja erros e dependências no script que podem fazer com que o script aguarde.

5. Se espera que as personalizações precisem de mais tempo, aumente [a buildTimeoutInMinutes](image-builder-json.md). O incumprimento é de quatro horas.

6. Se tiver ações intensivas em recursos, como o descarregamento de gigabytes de ficheiros, considere o tamanho VM de construção subjacente. O serviço utiliza um Standard_D1_v2 VM. O VM tem 1 vCPU e 3,5 GB de memória. Se estiver a descarregar 50 GB, isto provavelmente esgotará os recursos VM e causará falhas de comunicação entre o Serviço de Construtor de Imagem Azure e construirá VM. Redaçar a construção com um VM de memória maior, definindo o [VM_Size](image-builder-json.md#vmprofile).

### <a name="long-file-download-time"></a>Tempo de descarregamento de ficheiros longos

#### <a name="error"></a>Erro
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Causa 

O personalizador de ficheiros está a descarregar um ficheiro grande.

#### <a name="solution"></a>Solução

O personalizador de ficheiros só é adequado para pequenos downloads de ficheiros inferiores a 20 MB. Para transferências de ficheiros maiores, utilize um script ou um comando inline. Por exemplo, no Linux pode utilizar `wget` ou `curl` . No Windows, pode `Invoke-WebRequest` utilizar.

### <a name="error-waiting-on-shared-image-gallery"></a>Erro à espera na galeria de imagens partilhada

#### <a name="error"></a>Erro

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Causa

O Image Builder cronometrou à espera que a imagem fosse adicionada e replicada na Galeria de Imagens Partilhadas (SIG). Se a imagem está a ser injetada no SIG, pode presumir-se que a construção da imagem foi bem sucedida. No entanto, o processo global falhou, porque o construtor de imagens estava à espera da galeria de imagens partilhada para completar a replicação. Mesmo que a construção tenha falhado, a replicação continua. Pode obter as propriedades da versão de imagem verificando o *runOutout de* distribuição .

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Solução

Aumentar a **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Eventos de informação de recursos baixos do Windows

#### <a name="error"></a>Erro

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Causa

Exaustão de recursos. Este problema é geralmente visto com o Windows Update a funcionar utilizando o tamanho VM de construção padrão D1_V2.

#### <a name="solution"></a>Solução

Aumente o tamanho de VM de construção.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Constrói acabados, mas nenhum artefacto foi criado

#### <a name="error"></a>Erro

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Causa

Time out causado pela espera da criação dos recursos necessários do Azure.

#### <a name="solution"></a>Solução

Re-ensaio para tentar de novo.

### <a name="resource-not-found"></a>Recurso não encontrado

#### <a name="error"></a>Erro

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Causa

Falta de permissões.

#### <a name="solution"></a>Solução

Recheck Azure Image Builder tem todas as permissões necessárias. 

Para obter mais informações sobre permissões de configuração, consulte [permissões do Serviço de Construtores de Imagem Configure Azure usando permissões do Serviço de Construtores de Imagem Azure ou](image-builder-permissions-cli.md) [Configure Azure Image Builder](image-builder-permissions-powershell.md)

### <a name="sysprep-timing"></a>Tempo de Sysprep

#### <a name="error"></a>Erro

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Causa

A causa pode ser um problema de tempo devido ao tamanho D1_V2 VM. Se as personalizações forem limitadas e executadas em menos de três segundos, os comandos sysprep são executados pelo Azure Image Builder para desresvisão. Quando o Azure Image Builder desescciona, o comando sysprep verifica o *WindowsAzureGuestAgent*, que pode não estar totalmente instalado, causando o problema de tempo. 

#### <a name="solution"></a>Solução

Aumente o tamanho de VM. Ou, pode adicionar uma personalização do sono powerShell de 60 segundos para evitar o problema de tempo.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Cancelamento de construtor após contexto de cancelamento de contexto cancelado

#### <a name="error"></a>Erro
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Causa
O serviço Image Builder utiliza a porta 22 (Linux) ou 5986 (Windows) para ligar ao VM de construção, isto ocorre quando o serviço é desligado da construção VM durante uma construção de imagem. As razões para a desconexão podem variar, mas ativar ou configurar firewalls no script pode bloquear as portas acima.

#### <a name="solution"></a>Solução
Reveja os seus scripts para alterações/ativação de firewall, ou alterações no SSH ou WinRM, e certifique-se de que quaisquer alterações permitem uma conectividade constante entre o serviço e construa VM nas portas acima. Para obter mais informações sobre a rede do Image Builder, reveja os [requisitos.](./image-builder-networking.md)

## <a name="devops-task"></a>Tarefa de DevOps 

### <a name="troubleshooting-the-task"></a>Resolução de problemas da tarefa
A tarefa só falhará se ocorrer um erro durante a personalização, quando isso acontecer, a tarefa reportará falha e deixará o grupo de recursos de encenação, com os registos, para que possa identificar o problema. 

Para localizar o registo, você precisa saber o nome do modelo, entrar no pipeline > construção falhada > perfurar na tarefa de DevOps AIB, então você verá o registo e um nome de modelo:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Vá ao portal, procure o nome do modelo no grupo de recursos e, em seguida, procure o grupo de recursos com IT_*.
Vá à conta de armazenamento > bolhas > contentores > troncos.

### <a name="troubleshooting-successful-builds"></a>Resolução de problemas com sucesso constrói
Há talvez alguns casos em que precisa investigar construções bem sucedidas, e quer rever o registo. Como mencionado, se a construção de imagens for bem sucedida, o grupo de recursos de encenação que contém os registos será eliminado como parte da limpeza. No entanto, o que você pode fazer, é introduzir um sono após o comando inline, em seguida, obter os troncos à medida que a construção é pausada. Para isso siga estes passos:
 
1. Atualize o comando inline, e adicione: Write-Host / Eco "Sleep" – isto lhe permitirá pesquisar no registo
2. Adicione um sono durante pelo menos 10 mins, pode usar [o comando Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep)ou `Sleep` Linux.
3. Utilize o método acima para identificar a localização do registo e, em seguida, continue a descarregar/verificar o registo até que ele chegue ao sono.


### <a name="operation-was-canceled"></a>A operação foi cancelada.

#### <a name="error"></a>Erro

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Causa

Se a construção não foi cancelada por um utilizador, foi cancelada pelo Agente utilizador Azure DevOps. O mais provável é que o tempo limite de 1 hora tenha ocorrido devido às capacidades do Azure DevOps. Se estiver a usar um projeto privado e um agente, obtém 60 minutos de tempo de construção. Se a construção exceder o tempo limite, o DevOps cancela a tarefa de execução.

Para obter mais informações sobre as capacidades e limitações do Azure DevOps, consulte [os agentes hospedados pela Microsoft](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations)
 
#### <a name="solution"></a>Solução

Pode hospedar os seus próprios agentes DevOps ou procurar reduzir o tempo da sua construção. Por exemplo, se estiver a distribuir na galeria de imagens partilhada, replique-se para uma região. Se quiser replicar assíncroticamente. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Início de leitura do Windows Lento: "Por favor, aguarde o instalador de módulos windows"

#### <a name="error"></a>Erro
Depois de criar uma imagem do Windows 10 com o Image Builder, em seguida, crie um VM a partir da imagem, você RDP, e tenha que esperar minutos no primeiro logor o logo ver um ecrã azul com a mensagem:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Solução
Em primeiro lugar, na verificação de construção de imagens, não são necessárias reinicializações pendentes adicionando um personalizador Windows Restart como a última personalização, e que toda a instalação do software está completa. Por último, adicione [/mode:vm](/windows-hardware/manufacture/desktop/sysprep-command-line-options) opção ao sysprep padrão que o AIB utiliza, ver abaixo, 'VMs criados a partir de imagens AIB não criam com sucesso' > 'Overriding the Commands'  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>VMs criados a partir de imagens AIB não criam com sucesso

Por predefinição, o Azure Image Builder executa código *de desavisionamento* no final de cada fase de personalização de imagem para *generalizar* a imagem. Generalizar é um processo em que a imagem é configurada para ser reutilizada para criar vários VMs e pode passar em configurações VM, tais como nome de hospedeiro, nome de utilizador, etc. Para windows, O Azure Image Builder executa *o Sysprep*, e para o Linux Azure Image Builder executa `waagent -deprovision` . 

Para o Windows, o Azure Image Builder utiliza um comando genérico Sysprep. No entanto, isto pode não ser adequado para qualquer generalização bem sucedida do Windows. O Azure Image Builder permite-lhe personalizar o comando Sysprep. Note Azure Image Builder é uma ferramenta de automatização de imagem. É responsável por gerir o comando Sysprep com sucesso. Mas pode precisar de diferentes comandos Sysprep para tornar a sua imagem reutilizável. Para o Linux, o Azure Image Builder utiliza um `waagent -deprovision+user` comando genérico. Para mais informações, consulte [a documentação do Agente Microsoft Azure Linux](https://github.com/Azure/WALinuxAgent#command-line-options).

Se estiver a migrar uma personalização existente e estiver a utilizar diferentes comandos Sysprep/waagent, pode experimentar os comandos genéricos do construtor de imagens. Se a criação de VM falhar, use os comandos Sysprep/waagent anteriores.

> [!NOTE]
> Se o AIB criar uma imagem personalizada do Windows com sucesso e criar um VM a partir dele, então descubra que o VM não criará com sucesso (por exemplo, o comando de criação VM não completa/intervalos de tempo), terá de rever a documentação do Windows Server Sysprep. Ou, pode levantar um pedido de suporte com a equipa de Suporte ao Cliente do Windows Server Sysprep, que pode resolver problemas e aconselhar sobre o comando Sysprep correto.

### <a name="command-locations-and-filenames"></a>Locais de comando e filenames

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Comando Sysprep: Janelas

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Comando de deprovisionamento: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>Sobrevando os Comandos

Para anular os comandos, utilize os provisionadores de scripts PowerShell ou shell para criar os ficheiros de comando com o nome exato do ficheiro e colocá-los nos diretórios listados anteriormente. O Azure Image Builder lê estes comandos e a saída é escrita para a *personalização.log*.

## <a name="getting-support"></a>Obter Suporte
Se se referiu à orientação e ainda não consegue resolver o seu problema, pode abrir um caso de apoio. Ao fazê-lo, selecione o produto certo e o tópico de suporte, o que irá envolver a equipa de suporte do Azure VM Image Builder.

Selecionando o produto de caixa:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte [a visão geral do Azure Image Builder](image-builder-overview.md).
