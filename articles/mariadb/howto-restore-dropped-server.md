---
title: Restaurar uma base de dados de Azure eliminada para o servidor MariaDB
description: Este artigo descreve como restaurar um servidor eliminado na Base de Dados Azure para MariaDB usando o portal Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/12/2021
ms.openlocfilehash: 7cf2ce61239c7f2320f7b789acbb8f340e6de471
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315549"
---
# <a name="restore-a-deleted-azure-database-for-mariadb-server"></a>Restaurar uma base de dados de Azure eliminada para o servidor MariaDB

Quando um servidor é eliminado, a cópia de segurança do servidor de bases de dados pode ser mantida até cinco dias no serviço. A cópia de segurança da base de dados só pode ser acedida e restaurada a partir da subscrição do Azure, onde o servidor residia originalmente. Os seguintes passos recomendados podem ser seguidos para recuperar um recurso de servidor MariaDB eliminado no prazo de 5 dias a partir do momento da eliminação do servidor. Os passos recomendados só funcionarão se a cópia de segurança do servidor ainda estiver disponível e não tiver sido eliminada do sistema. 

## <a name="pre-requisites"></a>Pré-requisitos
Para restaurar uma Base de Dados Azure eliminada para o servidor MariaDB, precisa de ser:
- Nome de subscrição Azure que hospeda o servidor original
- Localização onde o servidor foi criado

## <a name="steps-to-restore"></a>Passos para restaurar

1. Aceda ao Registo de [Atividades](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) da lâmina monitor no portal Azure. 

2. No Registo de Atividades, clique em **Adicionar filtro** como mostrado e definir os seguintes filtros para o 

    - **Subscrição** = A sua subscrição que hospeda o servidor eliminado
    - **Tipo de recurso** = Base de Dados Azure para servidores MariaDB (Microsoft.DBForMariaDB/servidores) 
    - **Operação** = Eliminar o Servidor MariaDB (Microsoft.DBForMariaDB/servidores/delete) 
 
     [![Registo de atividade filtrado para eliminar a operação do servidor MariaDB](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Clique duas vezes no evento Delete MariaDB Server e clique no separador JSON e note os atributos "resourceId" e "submissãoTimestamp" na saída JSON. O resourceId está no seguinte formato: /subscrições/ffffff-ff-ff-ff-ffff-ffff-ffffff-ffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBForMariaDB/servers/deletedserver.
 
 4. Vá para criar a [página API do Servidor REST](/rest/api/mariadb/servers/create) e clique no separador "Experimente" realçado em verde e inicie sessão com a sua conta Azure.
 
 5. Forneça o nome do Grupo de Recursos, o nome do servidor (nome do servidor eliminado), a subscriçãoId, derivada do atributo resourceId capturado no Passo 3, enquanto a versão api é pré-povoada como mostrado na imagem.
 
     [![Criar servidor usando REST API](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Desloque-se abaixo na secção 'Request Body' e cole o seguinte:
     
    ```json
    {
        "location": "Dropped Server Location",  
        "properties": 
            {
                "restorePointInTime": "submissionTimestamp - 15 minutes",
                "createMode": "PointInTimeRestore",
                "sourceServerId": "resourceId"
            }
    }
    ```

7. Substitua os seguintes valores no organismo de pedido supri o anterior:
   * "Drop server Location" com a região Azure onde o servidor eliminado foi originalmente criado
   * "submissãoTimestamp", e "resourceId" com os valores capturados no Passo 3. 
   * Para "restaurar oPointInTime", especifique um valor de "submissãoTimestamp" menos **15 minutos** para garantir que o comando não se errar.

8. Se vir o Código de Resposta 201 ou 202, o pedido de restauro é submetido com sucesso. 

9. A criação do servidor pode demorar algum tempo dependendo do tamanho da base de dados e dos recursos de cálculo a provisionados no servidor original. O estado de restauração pode ser monitorizado a partir do registo de atividades através da filtragem 
   - **Assinatura** = A sua Assinatura
   - **Tipo de recurso** = Base de Dados Azure para servidores MariaDB (Microsoft.DBForMariaDB/servidores) 
   - **Operação** = Atualizar o servidor MariaDB Criar

## <a name="next-steps"></a>Passos seguintes
- Se estiver a tentar restaurar um servidor dentro de cinco dias, e ainda receber um erro depois de seguir com precisão os passos discutidos anteriormente, abra um incidente de suporte para assistência. Se estiver a tentar restaurar um servidor apagado após cinco dias, espera-se um erro, uma vez que o ficheiro de cópia de segurança não pode ser encontrado. Não abra um bilhete de apoio neste cenário. A equipa de apoio não pode prestar qualquer assistência se a cópia de segurança for eliminada do sistema. 
- Para evitar a eliminação acidental dos servidores, recomendamos vivamente a utilização [de Bloqueios de Recursos](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).
