---
title: Obter informações de estado para uma tarefa de importar/exportar do Azure | Documentos da Microsoft
description: Saiba como obter informações de estado de tarefas do serviço de importação/exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 1a878b5a9f0502ff9acd411359895d7431fb76f4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437247"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Obter informações de estado para uma tarefa de importação/exportação
Pode chamar o [Get Job de](/rest/api/storageimportexport/jobs) operação para obter informações sobre ambos, importar e exportar tarefas. As informações devolvidas incluem:

-   O estado atual da tarefa.

-   A percentagem de aproximado que cada tarefa foi concluída.

-   O estado atual de cada unidade.

-   URIs de blobs que contém os registos de erros e informações de registo verboso (se ativada).

As secções seguintes explicam as informações devolvidas pelo `Get Job` operação.

## <a name="job-states"></a>Estados de tarefa
A tabela e o diagrama de estado abaixo descrevem os Estados que uma tarefa se movimenta por durante seu ciclo de vida. O estado atual da tarefa pode ser determinado ao chamar o `Get Job` operação.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

A tabela seguinte descreve cada Estado em que uma tarefa pode pass-through.

|Estado da tarefa|Descrição|
|---------------|-----------------|
|`Creating`|Depois de chamar a operação de colocar a tarefa, é criada uma tarefa e respetivo estado é definido como `Creating`. Enquanto a tarefa está no `Creating` de estado, o serviço importar/exportar pressupõe que as unidades não foram enviadas para o Centro de dados. Uma tarefa pode permanecer no `Creating` estado durante até duas semanas, após o qual é automaticamente eliminado pelo serviço.<br /><br /> Se chamar a operação de atualização propriedades da tarefa enquanto a tarefa está no `Creating` de estado, a tarefa continuará a ser o `Creating` estado e o intervalo de tempo limite é reposta para duas semanas.|
|`Shipping`|Depois de enviar seu pacote, deve chamar a atualização de operação de propriedades da tarefa de atualizar o estado da tarefa para `Shipping`. O estado de envio pode ser definido apenas se o `DeliveryPackage` (operadora postal e número de controlo) e o `ReturnAddress` foram definidas propriedades para a tarefa.<br /><br /> A tarefa irá permanecer no estado de envio para duas semanas. Se duas semanas tenham passado e as unidades não foram recebidas, os operadores do serviço de importação/exportação serão notificados.|
|`Received`|Depois de todas as unidades forem recebidas no Centro de dados, o estado da tarefa será definido para o estado de recebidos.|
|`Transferring`|Depois das unidades de serem recebidas no Centro de dados e pelo menos uma unidade iniciou o processamento, o estado da tarefa será definido como o `Transferring` estado. Consulte o `Drive States` secção abaixo para obter informações detalhadas.|
|`Packaging`|Depois de concluíram todas as unidades de processamento, a tarefa será colocada no `Packaging` até que as unidades são fornecidas para o cliente de estado.|
|`Completed`|Depois de todas as unidades têm foi enviadas para o customer, se a tarefa for concluído sem erros, em seguida, a tarefa será definida como o `Completed` estado. A tarefa sejam eliminada automaticamente após 90 dias no `Completed` estado.|
|`Closed`|Depois de todas as unidades têm foi enviadas para o customer, se tiverem sido quaisquer erros durante o processamento da tarefa, em seguida, a tarefa será definida como o `Closed` estado. A tarefa sejam eliminada automaticamente após 90 dias no `Closed` estado.|

Pode cancelar uma tarefa apenas em determinados Estados. Uma tarefa foi cancelada ignora o passo de cópia de dados, mas caso contrário, ela segue as mesmo transições de estado como uma tarefa que não foi cancelada.

A tabela seguinte descreve os erros que podem ocorrer para cada Estado de tarefa, bem como o efeito sobre o trabalho quando ocorre um erro.

|Estado da tarefa|Evento|Resolução / próximos passos|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Uma ou mais unidades para uma tarefa chegaram, mas a tarefa não está no `Shipping` Estado ou não existe nenhum registo da tarefa no serviço.|A equipe de operações do serviço de importação/exportação irá tentar contactar os clientes para criar ou atualizar a tarefa com as informações necessárias para seguir em frente a tarefa.<br /><br /> Se a equipe de operações não consegue contactar os clientes em duas semanas, a equipe de operações irá tentar devolver as unidades.<br /><br /> No caso de não podem ser devolvidas as unidades e o cliente não pode ser alcançado, as unidades irão ser destruídas de forma segura em 90 dias.<br /><br /> Tenha em atenção que uma tarefa não pode ser processada até que o seu estado é atualizado para `Shipping`.|
|`Shipping`|O pacote para uma tarefa não está disponível durante mais de duas semanas.|A equipe de operações notificará o cliente do pacote em falta. Com base na resposta do cliente, a equipe de operações serão expandir o intervalo de aguardar que o pacote para chegam, ou cancelar a tarefa.<br /><br /> No caso do cliente não puderem ser contatado ou não responde dentro de 30 dias, a equipe de operações iniciará um ação para mover a tarefa a partir da `Shipping` Estado diretamente para o `Closed` estado.|
|`Completed/Closed`|As unidades nunca atingiu o endereço do remetente ou foram danificadas no envio (aplica-se apenas a uma tarefa de exportação).|Se as unidades não alcançar o endereço de retorno, o cliente deve chamar a operação Get Job pela primeira vez ou verificar o estado da tarefa no portal para se certificar de que as unidades têm foi enviadas. Se as unidades tem sido lançadas, os clientes devem contactar o fornecedor de envio para tentar localizar as unidades.<br /><br /> Se as unidades estão danificadas durante a remessa, o cliente poderá querer pedir outra tarefa de exportação, ou transferir os blobs em falta.|
|`Transferring/Packaging`|Tarefa tem um incorreto ou em falta o endereço de retorno.|A equipe de operações em contacto com a pessoa de contato para a tarefa para obter o endereço correto.<br /><br /> No caso do cliente não pode ser alcançado, as unidades irão ser destruídas de forma segura em 90 dias.|
|`Creating / Shipping/ Transferring`|Uma unidade que não aparece na lista de unidades para ser importado está incluída no pacote de envio.|As unidades de Extras não serão processadas e serão retornadas para o cliente quando a tarefa é concluída.|

## <a name="drive-states"></a>Estados de unidade
A tabela e o diagrama abaixo descrevem o ciclo de vida de uma unidade individual, como faz a transição por meio de uma tarefa de importação ou exportação. Pode recuperar o estado atual da unidade ao chamar o `Get Job` operação e analisando a `State` elemento do `DriveList` propriedade.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

A tabela seguinte descreve cada Estado, que pode passar uma unidade.

|Unidade de estado|Descrição|
|-----------------|-----------------|
|`Specified`|Para uma tarefa de importação, quando a tarefa é criada com a operação de colocar a tarefa, o estado inicial para uma unidade é o `Specified` estado. Para uma tarefa de exportação, uma vez que for especificada nenhuma unidade quando a tarefa é criada, o estado do disco inicial é o `Received` estado.|
|`Received`|A unidade faz a transição para o `Received` quando o operador de serviço de importação/exportação processou as unidades que foram recebidas da empresa de transporte para uma tarefa de importação de estado. Para uma tarefa de exportação, o estado da unidade inicial é o `Received` estado.|
|`NeverReceived`|A unidade irá mudar para o `NeverReceived` quando chega o pacote para uma tarefa, mas o pacote não contém a unidade de estado. Uma unidade também pode mover entraram neste estado se já tiverem passado duas semanas, uma vez que o serviço recebeu as informações de envio, mas o pacote ainda não está disponível no Centro de dados.|
|`Transferring`|Uma unidade será movido para o `Transferring` de estado quando o serviço começa transferir dados da unidade de armazenamento do Windows Azure.|
|`Completed`|Uma unidade será movido para o `Completed` quando o serviço foi transferida com êxito todos os dados sem erros de estado.|
|`CompletedMoreInfo`|Uma unidade será movido para o `CompletedMoreInfo` quando o serviço encontrou alguns problemas ao copiar dados a partir ou para a unidade de estado. As informações podem incluir erros, avisos ou mensagens informativas sobre Sobrescrever blobs.|
|`ShippedBack`|A unidade irá mudar para o `ShippedBack` estado quando ele foi enviado de parte de trás do Centro de dados para o endereço do remetente.|

A tabela seguinte descreve os Estados de falha de unidade e as ações executadas para cada Estado.

|Unidade de estado|Evento|Resolução / passo seguinte|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Uma unidade que está marcada como `NeverReceived` (porque não foi recebido como parte do envio da tarefa) é recebido no envio de outro.|A equipe de operações irá mover a unidade para o `Received` estado.|
|`N/A`|Uma unidade que não faz parte de qualquer tarefa chega ao centro de dados como parte de outra tarefa.|A unidade será marcada como uma unidade extra e será retornada para o cliente quando a tarefa associada ao pacote original é concluída.|

## <a name="faulted-states"></a>Estados com falhas
Quando uma tarefa ou a unidade de falha de avançar, normalmente por meio de seu ciclo de vida esperado, a tarefa ou a unidade será movida para um `Faulted` estado. Nesse ponto, a equipe de operações irá contactar os clientes por e-mail ou telefone. Assim que o problema for resolvido, será direcionada a tarefa com falha ou a unidade do `Faulted` estaduais e movido para o estado apropriado.

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar a API de REST do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
