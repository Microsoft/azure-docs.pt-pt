---
title: Serviço de Atestado Azure FPGFA
description: Serviço de atestação para os VMs da série NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 563155bb6559f8443f1453a65fa0b1574af106f7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556248"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Atestado FPGA para VMs NP-Series Azure (Pré-visualização)

O serviço de Attestation FPGA executa uma série de validações num ficheiro de verificação de design (chamado "netlist") gerado pelo conjunto de ferramentas Xilinx e produz um ficheiro que contém a imagem validada (chamada "bitstream") que pode ser carregada no cartão Xilinx U250 FPGA numa série VM da série NP.  

## <a name="prerequisites"></a>Pré-requisitos  

Você precisará de uma subscrição Azure e uma conta de Armazenamento Azure. A subscrição dá-lhe acesso ao Azure e a conta de armazenamento é usada para manter a sua lista de resultados e ficheiros de saída do serviço de atestado.  

Fornecemos scripts PowerShell e Bash para submeter pedidos de atestado.   Os scripts usam O CLI Azure, que pode ser executado no Windows e Linux. O PowerShell pode funcionar no Windows, Linux e macOS.  

Download do Azure CLI (obrigatório):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Download powerShell para Windows, Linux e macOS (apenas para scripts PowerShell):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Você precisará ter o seu inquilino e iD de assinatura autorizado a submeter-se ao serviço de atestado. Visita https://aka.ms/AzureFPGAAttestationPreview para solicitar acesso. 

## <a name="building-your-design-for-attestation"></a>Construindo o seu projeto para atestado  

O conjunto de ferramentas xilinx preferido para projetos de construção é Vitis 2020.2. Os ficheiros Netlist que foram criados com uma versão anterior do toolset e que ainda são compatíveis com 2020.2 podem ser utilizados. Certifique-se de que carregou a casca correta para construir contra. A versão atualmente suportada é xilinx_u250_gen3x16_xdma_2_1_202010_1. Os ficheiros de suporte podem ser descarregados a partir do salão Xilinx Alveo. 

Deve incluir o seguinte argumento ao Vitis (linha v++ cmd) para construir um ficheiro xclbin que contenha uma lista de netlist em vez de um bitstream.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Iniciar sessão em Azure  

Antes de realizar quaisquer operações com o Azure, deve iniciar sessão no Azure e definir a subscrição que está autorizada a ligar para o serviço. Use os ```az login``` comandos e ```az account set –s <Sub ID or Name>``` comandos para este fim. Mais informações sobre este processo são documentadas aqui:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Utilize a opção "sinal interativamente" ou "iniciar sin com credenciais" na linha de comando.  

## <a name="creating-a-storage-account-and-blob-container"></a>Criação de uma conta de armazenamento e recipiente de bolhas  

O seu ficheiro netlist deve ser enviado para um recipiente de blob de armazenamento Azure para acesso pelo serviço de atestado.  

Consulte esta página para obter mais informações sobre a criação da conta, um contentor e o upload da sua lista de netlist como uma bolha para aquele recipiente: https://docs.microsoft.com/azure/storage/blobs/storage-quickstartblobs-cli .  

Também pode utilizar o portal Azure para isto.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Faça o upload do seu ficheiro netlist para o armazenamento de blob Azure  

Existem várias formas de copiar o ficheiro; um exemplo utilizando o cmdlet de upload de armazenamento az é mostrado abaixo. Os comandos AZ funcionam tanto no Linux como no Windows. Pode escolher qualquer nome para o nome "blob", mas certifique-se de reter a extensão do xclbin. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Descarregue os scripts de atestado  

Os scripts de validação podem ser descarregados a partir do seguinte recipiente de armazenamento Azure:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

O ficheiro zip tem dois scripts PowerShell, um para submeter e o outro para monitorizar enquanto o terceiro ficheiro é um script de bash que executa ambas as funções.  

## <a name="running-the-attestation-scripts"></a>Executando os scripts do atestado  

Para executar os scripts, terá de fornecer o nome da sua conta de armazenamento, o nome do recipiente blob onde o ficheiro netlist está armazenado e o nome do ficheiro netlist. Também terá de criar uma assinatura de acesso partilhado (SAS) que conceda acesso de leitura/escrita ao seu contentor (não à lista de netlist). Este SAS é utilizado pelo serviço de atestado para fazer uma cópia local do seu ficheiro netlist e para retornar os ficheiros de saída resultantes do processo de validação para o seu contentor.  

Uma visão geral das assinaturas de acesso partilhado está disponível aqui com informações específicas sobre o Serviço SAS disponível aqui. A página DE Serviço SAS inclui uma importante cautela sobre a proteção do SAS gerado.  Leia a advertência para compreender a necessidade de manter o SAS protegido contra utilizações maliciosas ou não intencionais.  

Pode gerar um SAS para o seu recipiente utilizando o recipiente de armazenamento az generate-sas cmdlet. Especificar um prazo de validade no formato UTC que tenha pelo menos algumas horas de antecedência; cerca de 6 horas devem ser mais do que adequadas.  

Se desejar utilizar diretórios virtuais, deve incluir a hierarquia do diretório como parte do argumento do contentor. Por exemplo, se tiver um contentor chamado "netlists" e tiver um diretório virtual chamado "image1" que contenha a bolha da lista de rede, especificaria "netlists/image1" como o nome do recipiente. Anexar quaisquer nomes adicionais de diretório para especificar uma hierarquia mais profunda. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Verificando o estado da sua submissão  

O serviço atestado devolverá a identificação de orquestração da sua submissão. Os scripts de submissão começam automaticamente a monitorizar a submissão através de sondagens para conclusão. A identificação da orquestração é a principal forma de revermos o que aconteceu à sua submissão, por isso, por favor, guarde isso no caso de ter algum problema. Como pontos de referência, o atestado leva cerca de 30 minutos para ser concluído para um pequeno ficheiro netlist (tamanho 300MB); um ficheiro de 1,6GB demorou uma hora. 

Pode ligar para o Monitor-Validation.ps1 script a qualquer momento para obter o estado e os resultados do atestado, fornecendo o ID de orquestração como argumento:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Em alternativa, pode submeter o pedido de correio HTTP ao ponto final do serviço de atestado:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

O organismo de pedido deve conter o seu ID de assinatura, ID do inquilino e iD de orquestração do seu pedido de atestado:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Passos de validação pós-100

O serviço irá reescrevê-lo no seu recipiente. Se o passe de validação for bem sucedido, o seu contentor terá o ficheiro original netlist (abc.xclbin), um ficheiro com o bitstream (abc.bit.xclbin), um ficheiro que identifica a localização privada do seu bitstream armazenado (abc.azure.xclbin) e quatro ficheiros de registo: um para o processo de arranque (abc-log.txt) e um para cada uma das três fases paralelas que realizam a validação. Estes são chamados *logPhaseX.txt onde X é um número para a fase. O azure.xclbin é utilizado no seu VM para sinalizar o upload da sua imagem validada para o U250. 

Se a validação falhar, está escrito um ficheiro de erro**.txt indicando que passo falhou. Verifique também os ficheiros de registo se o registo de erros indica que o atestado falhou. Ao contactar-nos para obter apoio, certifique-se de incluir todos estes ficheiros como parte do pedido de apoio juntamente com o ID de orquestração.  

Pode utilizar o portal Azure para criar o seu contentor, bem como carregar a sua lista de net e descarregar os ficheiros bitstream e log. A apresentação de um pedido de atestado e a monitorização do seu progresso através do portal não são suportadas neste momento e devem ser feitas através de scripts como descrito acima. 

