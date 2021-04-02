---
title: Restaurar uma base de dados de Azure abandonada para o servidor MySQL
description: Este artigo descreve como restaurar um servidor abandonado na Base de Dados Azure para o MySQL utilizando o portal Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 34dddd8e5f3fb418fc7155630bf82a922e418402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657095"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>Restaurar uma base de dados de Azure abandonada para o servidor MySQL

Quando um servidor é largado, a cópia de segurança do servidor de bases de dados pode ser mantida até cinco dias no serviço. A cópia de segurança da base de dados só pode ser acedida e restaurada a partir da subscrição do Azure, onde o servidor residia originalmente. Pode seguir estes passos recomendados para recuperar um recurso do servidor MySQL removido no prazo de 5 dias a partir do momento da eliminação do servidor. Os passos recomendados só funcionarão se a cópia de segurança do servidor ainda estiver disponível e não tiver sido eliminada do sistema. 

## <a name="pre-requisites"></a>Pré-requisitos
Para restaurar uma base de dados Azure para o servidor MySQL, precisa de ser:
- Nome de subscrição Azure que hospeda o servidor original
- Localização onde o servidor foi criado

## <a name="steps-to-restore"></a>Passos para restaurar

1. Aceda ao Registo de [Atividades](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) da lâmina monitor no portal Azure. 

2. No Registo de Atividades, clique em **Adicionar filtro** como mostrado e definir os seguintes filtros para o 

    - **Subscrição** = A sua subscrição que hospeda o servidor eliminado
    - **Tipo de recurso** = Base de Dados Azure para servidores MySQL (Microsoft.DBforMySQL/servidores) 
    - **Funcionamento** = Eliminar o Servidor MySQL (Microsoft.DBforMySQL/servidores/delete) 
 
     [![Registo de atividade filtrado para eliminar a operação do servidor MySQL](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Clique duas vezes no evento Delete MySQL Server e clique no separador JSON e note os atributos "resourceId" e "submissãoTimestamp" na saída JSON. O resourceId está no seguinte formato: /subscrições/ffffff-ff-ff-ff-ffff-ffffff-ffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. Vá para criar a [página API do Servidor REST](/rest/api/mysql/servers/create) e clique no separador "Experimente" realçado em verde e inicie sessão com a sua conta Azure.
 
 5. Forneça o nome do Grupo de Recursos, o nome do servidor (nome do servidor eliminado), a subscriçãoId, derivada do atributo resourceId capturado no Passo 3, enquanto a versão api é pré-povoada como mostrado na imagem.
 
     [![Criar servidor usando REST API](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Desloque-se abaixo na secção 'Request Body' e cole o seguinte substituindo a "Localização do servidor Abandonado", "submissãoTimestamp" e "resourceId". Para "restaurar oPointInTime", especifique um valor de "submissãoTimestamp" menos **15 minutos** para garantir que o comando não se errar.
 
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

7. Se vir o Código de Resposta 201 ou 202, o pedido de restauro é submetido com sucesso. 

8. A criação do servidor pode demorar algum tempo dependendo do tamanho da base de dados e dos recursos de cálculo a provisionados no servidor original. O estado de restauração pode ser monitorizado a partir do registo de atividades através da filtragem 
   - **Assinatura** = A sua Assinatura
   - **Tipo de recurso** = Base de Dados Azure para servidores MySQL (Microsoft.DBforMySQL/servidores) 
   - **Operação** = Atualizar O Servidor MySQL Criar

## <a name="next-steps"></a>Passos seguintes
- Se estiver a tentar restaurar um servidor dentro de cinco dias, e ainda receber um erro depois de seguir com precisão os passos discutidos anteriormente, abra um incidente de suporte para assistência. Se estiver a tentar restaurar um servidor abandonado ao fim de cinco dias, espera-se um erro, uma vez que o ficheiro de cópia de segurança não pode ser encontrado. Não abra um bilhete de apoio neste cenário. A equipa de apoio não pode prestar qualquer assistência se a cópia de segurança for eliminada do sistema. 
- Para evitar a eliminação acidental dos servidores, recomendamos vivamente a utilização [de Bloqueios de Recursos](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).