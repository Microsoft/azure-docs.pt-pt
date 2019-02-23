---
title: Substituir um disco físico no Azure Stack | Documentos da Microsoft
description: Descreve o processo para como substituir um disco físico no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 4fb2a398baa306cf9303284526bb43cd7f778441
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734630"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Substituir um disco físico no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo descreve o processo geral para substituir um disco físico no Azure Stack. Se um disco físico falhar, deverá substituí-la logo que possível.

Pode utilizar este procedimento para os sistemas integrados e para as implementações do kit de desenvolvimento com discos de fontes.

Substituição de disco real passos irão variar com base no seu fornecedor de hardware do fabricante de equipamento original (OEM). Consulte a documentação do fornecedor campo unidade substituível em (FRU) para obter passos detalhados que são específicas para o seu sistema.

## <a name="review-disk-alert-information"></a>Reveja as informações de alerta de disco
Quando um disco falha, receberá um alerta que indica que conectividade foi perdida a um disco físico.

 ![Disco físico perdeu a conectividade de exibição de alerta](media/azure-stack-replace-disk/DiskAlert.png)

Se abrir o alerta, a descrição do alerta contém o nó de unidade de escala e a localização exata de bloco físico para o disco que tem de substituir. Ainda mais o Azure Stack ajuda-o a identificar o disco com falha através de capacidades de indicador LED.

## <a name="replace-the-disk"></a>Substituir o disco

Siga as instruções de FRU do seu fornecedor de hardware de OEM para substituição de disco real.

> [!note]
> Substitua discos para um nó de unidade de escala por vez. Aguarde que as tarefas de reparo de disco virtual concluir antes de passar para o próximo nó de unidade de escala

Para impedir a utilização de um disco não suportado num sistema integrado, o sistema bloqueia discos que não são suportados pelo seu fornecedor. Se tentar usar um disco não suportado, um novo alerta indica que um disco tem sido colocado em quarentena devido a um modelo não suportado ou firmware.

Depois de substituir o disco, o Azure Stack é automaticamente Deteta o novo disco e inicia o processo de reparação do disco virtual.
 
## <a name="check-the-status-of-virtual-disk-repair"></a>Verificar o estado de reparação de disco virtual
 
 Depois de substituir o disco, pode monitorizar o estado de funcionamento de disco virtual e reparar o progresso da tarefa, utilizando o ponto final com privilégios. Siga estes passos em qualquer computador que tem conectividade de rede para o ponto final com privilégios.

1. Abra uma sessão do Windows PowerShell e ligue-se para o ponto final com privilégios.
    ```PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. Execute o seguinte comando para ver o estado de funcionamento do disco virtual:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![Saída do PowerShell do comando Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Execute o seguinte comando para ver o estado atual da tarefa de armazenamento:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Saída do PowerShell do comando Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Resolver problemas de reparação de disco virtual

Se o disco virtual reparar tarefa aparece bloqueada, execute o seguinte comando para reiniciar a tarefa:
  ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
