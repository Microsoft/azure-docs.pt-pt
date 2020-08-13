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
ms.openlocfilehash: 127604264850f9845846d0bb6a2768cac23cdc8c
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88169159"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Ciclo de vida de máquinas virtuais e estados

As Máquinas Virtuais Azure (VMs) passam por diferentes estados que podem ser categorizados em estados *de fornecimento* e *energia.* O objetivo deste artigo é descrever estes estados e destacar especificamente quando os clientes são cobrados, por exemplo, o uso. 

## <a name="power-states"></a>Estados de energia

O estado de poder representa o último estado conhecido do VM.

![Diagrama do estado de potência VM](./media/vm-power-states.png)

<br>
A tabela seguinte fornece uma descrição de cada estado de instância e indica se é faturada, por exemplo, a utilização ou não.

<table>
<tr>
<th>
Estado
</th>
<th>
Descrição
</th>
<th>
Faturação de utilização de instância
</th>
</tr>
<tr>
<td>
<p><b>A iniciar</b></p>
</td>
<td>
<p>O VM está a começar.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Não cobrado</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Em Execução</b></p>
</td>
<td>
<p>Estado de trabalho normal para um VM</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Faturado</b></p>
</td>
</tr>
<tr>
<td>
<p><b>A parar</b></p>
</td>
<td>
<p>Este é um estado de transição. Quando estiver concluído, mostrará como **Stop**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Faturado</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Parada</b></p>
</td>
<td>
<p>O VM foi desligado a partir do so do hóspede ou utilizando as APIs PowerOff.</p>
<p>O hardware ainda está atribuído ao VM e permanece no anfitrião. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>&#42;faturado </b></p>
</td>
</tr>
<tr>
<td>
<p><b>A desalocar</b></p>
</td>
<td>
<p>Estado de transição. Quando concluído, o VM mostrará como **Deallocated**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Não cobrado&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Desalocada</b></p>
</td>
<td>
<p>O VM foi interrompido com sucesso e removido do hospedeiro. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Não cobrado</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Alguns recursos Azure, tais como Discos e Networking, incorrem em encargos. As licenças de software no caso não incorrem em encargos.

## <a name="provisioning-states"></a>Estados de provisionamento

Um estado de provisionamento é o estado de uma operação de avião de controlo iniciada pelo utilizador no VM. Estes estados estão separados do estado de energia de um VM.

- **Criar** – Criação VM.

- **Update** – atualiza o modelo para um VM existente. Algumas alterações não-modelo para VM como Start/Restart também estão em atualização.

- **Excluir** – Eliminação de VM.

- **Deallocate** – é onde um VM é parado e removido do hospedeiro. A deallocação de um VM é considerada uma atualização, pelo que apresentará estados de provisionamento relacionados com a atualização.



Aqui estão os estados de operação transitórios após a plataforma ter aceite uma ação iniciada pelo utilizador:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Estados</b></p>
</td>
<td width="366">
<p>Descrição</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Criação</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Atualização</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Eliminar</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Estados de provisão de SO</b></p>
</td>
<td width="366">
<p>Se um VM for criado com uma imagem de SO e não com uma imagem especializada, então os subestados seguintes podem ser observados:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; O VM está em execução, e a instalação do SO convidado está em andamento. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Estado de curta duração. O VM transita rapidamente para **o Sucesso,** a menos que sejam necessárias extensões. A instalação de extensões pode demorar algum tempo. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Nota:</b>O provisionamento do SO pode transitar para **Falhado** se houver uma falha de SO ou o SO não for instalado a tempo. Os clientes serão cobrados para o VM implantado na infraestrutura.</p>
</td>
</tr>
</table>


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

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a monitorização do seu VM, consulte [as máquinas virtuais Monitor em Azure.](../azure-monitor/insights/monitor-vm-azure.md)