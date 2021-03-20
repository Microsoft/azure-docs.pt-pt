---
title: Restaurar uma base de dados de Azure abandonada para o servidor PostgreSQL
description: Este artigo descreve como restaurar um servidor abandonado na Base de Dados Azure para PostgreSQL usando o portal Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652930"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Restaurar uma base de dados de Azure abandonada para o servidor PostgreSQL

Quando um servidor é largado, a cópia de segurança do servidor de bases de dados pode ser mantida até cinco dias no serviço. A cópia de segurança da base de dados só pode ser acedida e restaurada a partir da subscrição do Azure, onde o servidor residia originalmente. Os seguintes passos recomendados podem ser seguidos para recuperar um recurso de servidor PostgreSQL abandonado no prazo de 5 dias a partir do momento da eliminação do servidor. Os passos recomendados só funcionarão se a cópia de segurança do servidor ainda estiver disponível e não tiver sido eliminada do sistema. 

## <a name="pre-requisites"></a>Pré-requisitos
Para restaurar uma base de dados de Azure para o servidor PostgreSQL, precisa de ser:
- Nome de subscrição Azure que hospeda o servidor original
- Localização onde o servidor foi criado

## <a name="steps-to-restore"></a>Passos para restaurar

1. Navegue pelo [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Selecione o serviço **Azure Monitor** e, em seguida, selecione **Registo de Atividades**.

2. No Registo de Atividades, clique no **filtro Adicionar** como mostrado e definido os seguintes filtros para os seguintes

    - **Subscrição** = A sua subscrição que hospeda o servidor eliminado
    - **Tipo de recurso** = Base de Dados Azure para servidores PostgreSQL (Microsoft.DBforPostgreSQL/servidores)
    - **Funcionamento** = Eliminar o Servidor PostgreSQL (Microsoft.DBforPostgreSQL/servidores/delete)
 
    ![Registo de atividade filtrado para eliminar a operação do servidor PostgreSQL](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Selecione o evento **'Eliminar o Servidor PostgreSQL'** e, em seguida, selecione o **separador JSON**. Copie os `resourceId` atributos e `submissionTimestamp` atributos na saída JSON. O resourceId está no seguinte formato: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Navegue na página API do [Pós-Venda do Servidor E](/rest/api/PostgreSQL/servers/create) selecione o **separador Try It** realçado em verde. Inscreva-se na sua conta Azure.

 5. Forneça o **recursoGroupName**, **serverName** (nome do servidor eliminado), propriedades **subscriçid,** com base no valor JSON do atributo de recursos JSON capturado no passo anterior 3. A propriedade versão api é pré-povoada e pode ser deixada como-é, como mostrado na imagem seguinte.

    ![Criar servidor usando REST API](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
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

    Por exemplo, se a hora atual for 2020-11-02T23:59:59.0000000Z, recomendamos um mínimo de 15 minutos antes do ponto de restauro no tempo 2020-11-02T23:44:59.000000Z.

    > [!Important]
    > Há um limite de tempo de cinco dias após o servidor ter sido largado. Após cinco dias, espera-se um erro, uma vez que o ficheiro de cópia de segurança não pode ser encontrado.
    
7. Se vir o Código de Resposta 201 ou 202, o pedido de restauro é submetido com sucesso. 

    A criação do servidor pode demorar algum tempo dependendo do tamanho da base de dados e dos recursos de cálculo a provisionados no servidor original. O estado de restauração pode ser monitorizado a partir do registo de atividades através da filtragem 
   - **Assinatura** = A sua Assinatura
   - **Tipo de recurso** = Base de Dados Azure para servidores PostgreSQL (Microsoft.DBforPostgreSQL/servidores) 
   - **Funcionamento** = Atualização pós-venda do servidor de postgresql criar

## <a name="next-steps"></a>Passos seguintes
- Se estiver a tentar restaurar um servidor dentro de cinco dias, e ainda receber um erro depois de seguir com precisão os passos discutidos anteriormente, abra um incidente de suporte para assistência. Se estiver a tentar restaurar um servidor abandonado ao fim de cinco dias, espera-se um erro, uma vez que o ficheiro de cópia de segurança não pode ser encontrado. Não abra um bilhete de apoio neste cenário. A equipa de apoio não pode prestar qualquer assistência se a cópia de segurança for eliminada do sistema. 
- Para evitar a eliminação acidental dos servidores, recomendamos vivamente a utilização [de Bloqueios de Recursos](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).
