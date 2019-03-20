---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125108"
---
Máquinas virtuais do Azure (VMs) passar por Estados diferentes que podem ser categorizados em *aprovisionamento* e *power* Estados. O objetivo deste artigo é descrever esses Estados e realçar especificamente quando os clientes são faturados por exemplo utilização. 

## <a name="power-states"></a>Estados de energia

O estado de energia representa o último estado conhecido da VM.

![Diagrama de estado de energia VM](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
A tabela seguinte fornece uma descrição de cada Estado de instância e indica se ele é faturado pela utilização de instância ou não.

<table>
<tr>
<th>
Estado
</th>
<th>
Descrição
</th>
<th>
Faturação da utilização de instância
</th>
</tr>
<tr>
<td>
<p><b>A partir de</b></p>
</td>
<td>
<p>VM está a ser iniciado.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Não lhe é faturada</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Em execução</b></p>
</td>
<td>
<p>Estado de funcionamento normal para uma VM</p>
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
<p>Este é o estado de transição. Quando concluída, será mostrado como **parado**.</p>
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
<p><b>Parado</b></p>
</td>
<td>
<p>A VM foi encerrada para baixo de dentro do SO convidado ou com as APIs de desligado.</p>
<p>Hardware ainda está alocado para a VM e permanece no anfitrião. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Faturado&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>A desalocar</b></p>
</td>
<td>
<p>Estado de transição. Quando concluída, a VM será mostrado como **Desalocado**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Não lhe é faturada&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Desalocada</b></p>
</td>
<td>
<p>A VM foi parada com êxito e removida do anfitrião. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Não lhe é faturada</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Alguns recursos do Azure, tais como discos e redes, incorrer em custos. Licenças de software na instância não incorrem em custos.

## <a name="provisioning-states"></a>A aprovisionar estados

Estado de aprovisionamento é o estado de uma operação iniciada pelo utilizador, plano de controlo na VM. Esses Estados são separados do Estado de energia de uma VM.

- **Criar** – a criação da VM.

- **Atualização** – atualiza o modelo para uma VM existente. Alguns não modelo muda a VM, como o início/reinicialização também se enquadram na atualização.

- **Eliminar** – eliminação da VM.

- **Desalocar** – é onde uma VM é parada e removida do anfitrião. A desalocar uma VM é considerada uma atualização, para que ele irá apresentar Estados de aprovisionamento relacionados com a atualização.



Aqui estão os Estados de transição de operação depois da plataforma aceitou uma ação iniciada pelo utilizador:

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
<p><b>A criar</b></p>
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
<p><b>Updating</b></p>
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
<p><b>A eliminar</b></p>
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
<p><b>Estados de aprovisionamento do SO</b></p>
</td>
<td width="366">
<p>Se uma VM for criada com uma imagem de SO e não com uma imagem especializada, podem ser observados substates seguintes:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; a VM está em execução e instalação do SO convidado está em curso. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; estado de curta duração. A VM passa rapidamente para **êxito** , a menos que quaisquer extensões têm de ser instalados. Instalar extensões pode demorar tempo. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Nota</b>: Aprovisionamento do SO pode fazer a transição para **falhada** se ocorrer uma falha de sistema operacional ou o sistema operacional não for instalado no tempo. Os clientes serão faturados pela VM implantada na infraestrutura.</p>
</td>
</tr>
</table>


Quando a operação estiver concluída, a VM irão transitar para um dos seguintes Estados:

- **Foi efetuada com êxito** – concluíram as ações iniciadas pelo usuário.

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

 

- **Falha ao** – representa uma operação que falhou. Consulte os códigos de erro para obter mais informações e possíveis soluções.

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

A vista de instância API fornece as informações de estado de execução da VM. Para obter mais informações, consulte a [máquinas virtuais - vista de instância](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) documentação da API.

O Explorador de recursos do Azure fornece uma interface do Usuário simple para ver a Estado de execução de VM: [Explorador de recursos](https://resources.azure.com/).

Estados de aprovisionamento são visíveis nas propriedades da VM e vista de instância. Estados de energia estão disponíveis na vista de instância de VM. 

