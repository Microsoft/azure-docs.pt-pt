---
title: Ciclo de vida e estados de um VM em Azure
description: Visão geral do ciclo de vida de um VM em Azure, incluindo descrições dos vários estados em que um VM pode estar a qualquer momento.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: cd104a31fdba932481889db198ae3cc4998a4466
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88261893"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Ciclo de vida de máquinas virtuais e estados

As Máquinas Virtuais Azure (VMs) passam por diferentes estados que podem ser categorizados em estados *de fornecimento* e *energia.* O objetivo deste artigo é descrever estes estados e destacar especificamente quando os clientes são cobrados, por exemplo, o uso. 

## <a name="power-states"></a>Estados de energia

O estado de poder representa o último estado conhecido do VM.

![Diagrama do estado de potência VM](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
A tabela seguinte fornece uma descrição de cada estado de instância e indica se é faturada, por exemplo, a utilização ou não.

:::row:::
   :::column span="":::

   **Estado**
   
   :::column-end:::
   :::column span="":::

   **Descrição**

   :::column-end:::
   :::column span="":::

   **Taxa de utilização de instâncias**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **A iniciar**

   :::column-end:::
   :::column span="":::

   O VM está a começar.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Não cobrado**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Em Execução**

   :::column-end:::
   :::column span="":::

   Estado de trabalho normal para um VM

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Faturado**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **A parar**

   :::column-end:::
   :::column span="":::

   Este é um estado de transição. Quando estiver concluído, mostrará como **Stop**.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Faturado**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Parada**

   :::column-end:::
   :::column span="":::

   O VM foi desligado a partir do so do hóspede ou utilizando as APIs PowerOff.

   O hardware ainda está atribuído ao VM e permanece no anfitrião.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Faturado***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **A desalocar**

   :::column-end:::
   :::column span="":::

   Estado de transição. Quando concluído, o VM mostrará como **Deallocated**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Não cobrado***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Desalocada**

   :::column-end:::
   :::column span="":::

   O VM foi interrompido com sucesso e removido do hospedeiro.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Não cobrado**

   :::column-end:::
:::row-end:::


&#42; Alguns recursos Azure, tais como Discos e Networking, incorrem em encargos. As licenças de software no caso não incorrem em encargos.

## <a name="provisioning-states"></a>Estados de provisionamento

Um estado de provisionamento é o estado de uma operação de avião de controlo iniciada pelo utilizador no VM. Estes estados estão separados do estado de energia de um VM.

- **Criar** – Criação VM.

- **Update** – atualiza o modelo para um VM existente. Algumas alterações não-modelo para VM como Start/Restart também estão em atualização.

- **Excluir** – Eliminação de VM.

- **Deallocate** – é onde um VM é parado e removido do hospedeiro. A deallocação de um VM é considerada uma atualização, pelo que apresentará estados de provisionamento relacionados com a atualização.



Aqui estão os estados de operação transitórios após a plataforma ter aceite uma ação iniciada pelo utilizador:

:::row:::
   :::column span="":::

   **Estado**
   
   :::column-end:::
   :::column span="2":::

   **Descrição**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Criação**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Atualização**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Eliminar**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Estados de provisão de SO**
   
   :::column-end:::
   :::column span="2":::

   **Descrição**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Se um VM for criado com uma imagem de SO e não com uma imagem especializada, então os subestados seguintes podem ser observados:

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   O VM está em execução, e a instalação do SO convidado está em andamento.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   Estado de curta duração. O VM transita rapidamente para **o Sucesso,** a menos que sejam necessárias extensões. A instalação de extensões pode demorar algum tempo.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Nota:** O provisionamento do SO pode transitar para **Falhado** se houver uma falha de SO ou o SO não for instalado a tempo. Os clientes serão cobrados para o VM implantado na infraestrutura.

   :::column-end:::

:::row-end:::

Uma vez concluída a operação, o VM transitará para um dos seguintes estados:

- **Bem sucedidos** – as ações iniciadas pelo utilizador foram concluídas.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Falhado** – representa uma operação falhada. Consulte os códigos de erro para obter mais informações e possíveis soluções.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Vista de instância VM

A api de opinião de exemplo fornece informações sobre o estado de execução da VM. Para mais informações, consulte as [Máquinas Virtuais - Exemplo Ver](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) documentação da API.

O explorador de Recursos Azure fornece uma UI simples para visualizar o estado de execução VM: [Resource Explorer](https://resources.azure.com/).

Os estados de provisionamento são visíveis nas propriedades de VM e na visualização de instâncias. Os estados de energia estão disponíveis, por exemplo, à vista de VM.

Para recuperar o estado de potência de todos os VMs na sua subscrição, utilize as [Máquinas Virtuais - Liste Todas as API](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) com o status de **parâmetros Definidos** para *verdadeiros*.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a monitorização do seu VM, consulte [as máquinas virtuais Monitor em Azure.](../azure-monitor/insights/monitor-vm-azure.md)