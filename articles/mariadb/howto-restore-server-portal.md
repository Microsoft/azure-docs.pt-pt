---
title: Backup e restauro - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como restaurar um servidor na Base de Dados Azure para MariaDB usando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: fa8ead8daa202f5747c134a62fbd43bcdf2af0d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369247"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>Como fazer backup e restaurar um servidor na Base de Dados Azure para o MariaDB utilizando o portal Azure

## <a name="backup-happens-automatically"></a>A cópia de segurança acontece automaticamente
A Base de Dados Azure para servidores MariaDB é apoiada periodicamente para permitir as funcionalidades de Restauro. Utilizando esta funcionalidade, poderá restaurar o servidor e todas as suas bases de dados num momento anterior, num novo servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma [Base de Dados Azure para servidor mariaDB e base de dados](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Definir configuração de backup

Você faz a escolha entre configurar o seu servidor para backups redundantes localmente ou backups geograficamente redundantes na criação do servidor, na janela **Nível de Preços.**

> [!NOTE]
> Depois de criado um servidor, o tipo de redundância que tem, geograficamente redundante vs localmente redundante, não pode ser trocado.
>

Ao criar um servidor através do portal Azure, a janela **De Nível de Preços** é onde seleciona backups **localmente redundantes** ou **geograficamente redundantes** para o seu servidor. Esta janela também é onde seleciona o Período de **Retenção** de Backup - quanto tempo (em número de dias) deseja que as cópias de segurança do servidor sejam armazenadas.

   ![Nível de preços - Escolha Redundância de Backup](./media/howto-restore-server-portal/pricing-tier.png)

Para obter mais informações sobre a definição destes valores durante a criação, consulte a Base de [Dados Azure para](quickstart-create-mariadb-server-database-using-azure-portal.md)o quickstart do servidor MariaDB .

O período de retenção de cópia de segurança pode ser alterado num servidor através dos seguintes passos:
1. Assine no [portal Azure.](https://portal.azure.com/)

2. Selecione a sua Base de Dados Azure para o servidor MariaDB. Esta ação abre a página **de visão geral.**

3. Selecione **Nível** de Preços a partir do menu, em **DEFINIÇÕES**. Utilizando o slider, pode alterar o Período de **Retenção** de Backup para a sua preferência entre 7 e 35 dias.
Na imagem abaixo foi aumentada para 35 dias.
![Período de retenção de backup aumentado](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Clique **em OK** para confirmar a alteração.

O período de retenção de reserva rege o quão longe no tempo um restauro pontual pode ser recuperado, uma vez que é baseado em backups disponíveis. A restauração ponto-a-tempo é descrita mais na secção seguinte. 

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
A Base de Dados Azure para O DNDB permite-lhe restaurar o servidor de volta a um ponto-a-tempo e a uma nova cópia do servidor. Pode utilizar este novo servidor para recuperar os seus dados ou ter as suas aplicações de cliente apontadas para este novo servidor.

Por exemplo, se uma mesa foi acidentalmente largada ao meio-dia de hoje, você poderia restaurar o tempo pouco antes do meio-dia e recuperar a tabela e os dados em falta da nova cópia do servidor. A restauração pontual está ao nível do servidor, não ao nível da base de dados.

Os seguintes passos restauram o servidor da amostra para um ponto no tempo:
1. No portal Azure, selecione a sua Base de Dados Azure para o servidor MariaDB. 

2. Na barra de ferramentas da página **de visão geral** do servidor, selecione **Restaurar**.

   ![Base de Dados Azure para MariaDB - Visão geral - Restaurar botão](./media/howto-restore-server-portal/2-server.png)

3. Preencha o formulário Restaurar com as informações necessárias:

   ![Base de Dados Azure para MariaDB - Restaurar informações](./media/howto-restore-server-portal/3-restore.png)
   - **Ponto de restauro**: Selecione o ponto-a-tempo a que pretende restaurar.
   - **Servidor de destino**: Forneça um nome para o novo servidor.
   - **Localização**: Não é possível selecionar a região. Por predefinição é o mesmo que o servidor de origem.
   - **Nível**de preços: Não é possível alterar estes parâmetros ao fazer uma restauração pontual. É igual ao servidor de origem. 

4. Clique em **OK** para restaurar o servidor para restaurar um ponto-em-tempo. 

5. Assim que o restauro terminar, localize o novo servidor que é criado para verificar se os dados foram restaurados como esperado.


O novo servidor criado por restauração pontual tem o mesmo nome de login de servidor e senha que era válido para o servidor existente no momento escolhido. Pode alterar a palavra-passe a partir da página **'Overview'** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras têm de ser criadas separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Geo restaurar

Se configurar o seu servidor para backups geograficamente redundantes, um novo servidor pode ser criado a partir da cópia de segurança desse servidor existente. Este novo servidor pode ser criado em qualquer região que o Azure Database para MariaDB esteja disponível.  

1. Selecione **Bases de** > **dados Azure Base de dados para MariaDB**. Também pode escrever **MariaDB** na caixa de pesquisa para encontrar o serviço.

   ![A opção "Base de Dados Azure para MariaDB"](./media/howto-restore-server-portal/2_navigate-to-mariadb.png)

2. No formulário **Select Source** dropdown, escolha **Backup**. Esta ação carrega uma lista de servidores que têm cópias de segurança georedundantes ativadas. Selecione uma destas cópias de segurança para ser a fonte do seu novo servidor.
   ![Fonte selecionada: Backup e lista de backups georedundantes](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Quando um servidor é criado pela primeira vez, pode não estar imediatamente disponível para restauro geo. Pode levar algumas horas para que os metadados necessários sejam povoados.
   >

3. Preencha o resto do formulário com as suas preferências. Pode selecionar qualquer **Local**. Depois de selecionar a localização, pode selecionar o **Nível de Preços**. Por predefinição, os parâmetros para o servidor existente de onde está a restaurar são apresentados. Pode clicar em **OK** sem fazer alterações para herdar essas definições. Ou pode alterar a **Geração Compute** (se disponível na região que escolheu), número de **vCores,** Período de **Retenção**de Backup e Opção de **Redundância de Backup**. Não é suportado **o nível** de alteração dos preços (Básico, Propósito Geral ou Tamanho de **Armazenamento)** ou armazenamento durante a restauração.

O novo servidor criado pela Geo Restore tem o mesmo nome de login de administrador do servidor e senha que era válido para o servidor existente no momento em que o restauro foi iniciado. A palavra-passe pode ser alterada a partir da página **de visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem os pontos finais do serviço VNet que existiam no servidor original. Estas regras têm de ser criadas separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [backups](concepts-backup.md) do serviço
- Saiba mais sobre [réplicas](concepts-read-replicas.md)
- Saiba mais sobre opções [de continuidade do negócio](concepts-business-continuity.md)
