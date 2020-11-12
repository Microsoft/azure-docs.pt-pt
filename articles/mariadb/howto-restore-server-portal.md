---
title: Backup e restauro - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como restaurar um servidor na Base de Dados Azure para MariaDB usando o portal Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 4a5f2cc4cbf73f5c13533a94f1454022d3538880
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539630"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>Como fazer backup e restaurar um servidor na Base de Dados Azure para MariaDB usando o portal Azure

## <a name="backup-happens-automatically"></a>A cópia de segurança acontece automaticamente
A base de dados Azure para servidores MariaDB é monitorizada periodicamente para ativar as funcionalidades de Restauro. Utilizando esta funcionalidade, poderá restaurar o servidor e todas as suas bases de dados num ponto-a-tempo anterior, num novo servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para servidor e base de dados MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Configuração de backup de definição

Você faz a escolha entre configurar o seu servidor para backups locais redundantes ou backups geograficamente redundantes na criação do servidor, na janela **Priceing Tier.**

> [!NOTE]
> Depois de um servidor ser criado, o tipo de redundância que tem, geograficamente redundante vs localmente redundante, não pode ser trocado.
>

Ao criar um servidor através do portal Azure, a janela **Priceing Tier** é onde seleciona backups **locais redundantes** ou **geograficamente redundantes** para o seu servidor. Esta janela é também onde seleciona o **Período de Retenção de Cópia** de Segurança - quanto tempo (em número de dias) pretende que as cópias de segurança do servidor são armazenadas.

   ![Nível de Preços - Escolha redundância de backup](./media/howto-restore-server-portal/pricing-tier.png)

Para obter mais informações sobre a definição destes valores durante a criação, consulte a [Base de Dados Azure para o arranque rápido do servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

O período de retenção de backup pode ser alterado num servidor através dos seguintes passos:
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione a sua Base de Dados Azure para o servidor MariaDB. Esta ação abre a página **de visão geral.**

3. Selecione **O Nível de Preços** do menu, em **DEFINIÇÕES**. Utilizando o slider pode alterar o **Período de Retenção de Cópia** de Segurança para a sua preferência entre 7 e 35 dias.
Na imagem abaixo foi aumentado para 35 dias.
![Período de retenção de backup aumentado](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Clique **em OK** para confirmar a alteração.

O período de retenção de backups regula o quão longe no tempo um restauro de ponto no tempo pode ser recuperado, uma vez que é baseado em backups disponíveis. A restauração pontual é descrita ainda mais na secção seguinte. 

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
A Azure Database for MariaDB permite-lhe restaurar o servidor de volta a um ponto no tempo e para uma nova cópia do servidor. Pode utilizar este novo servidor para recuperar os seus dados ou ter as aplicações do seu cliente apontadas para este novo servidor.

Por exemplo, se uma mesa foi acidentalmente largada ao meio-dia de hoje, você poderia restaurar a hora pouco antes do meio-dia e recuperar a mesa e dados desaparecidos dessa nova cópia do servidor. A restauração pontual está ao nível do servidor, não ao nível da base de dados.

Os seguintes passos restauram o servidor da amostra num ponto no tempo:
1. No portal Azure, selecione a sua Base de Dados Azure para o servidor MariaDB. 

2. Na barra de ferramentas da página **'Visão Geral'** do servidor, selecione **'Restaurar'.**

   ![Base de Dados Azure para MariaDB - Visão geral - Botão de restauro](./media/howto-restore-server-portal/2-server.png)

3. Preencha o formulário Restaurar com as informações necessárias:

   ![Azure Database for MariaDB - Restaurar informações](./media/howto-restore-server-portal/3-restore.png)
   - **Ponto de restauro** : Selecione o ponto a tempo a que pretende restabelecer.
   - **Servidor-alvo** : Forneça um nome para o novo servidor.
   - **Localização** : Não é possível selecionar a região. Por padrão, é o mesmo que o servidor de origem.
   - **Nível de preços** : Não é possível alterar estes parâmetros ao fazer uma restauração pontual. É igual ao servidor de origem. 

4. Clique **em OK** para restaurar o servidor para restaurar a um ponto no tempo. 

5. Assim que a restauração terminar, localize o novo servidor que foi criado para verificar se os dados foram restaurados como esperado.

O novo servidor criado por point-in-time restore tem o mesmo nome de login de administrador do servidor e palavra-passe que era válido para o servidor existente no ponto no tempo escolhido. Pode alterar a palavra-passe a partir da página **de Visão Geral** do novo servidor.

O novo servidor criado durante uma restauração não possui os pontos finais do serviço VNet que existiam no servidor original. Estas regras têm de ser configuradas separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Restauro geo

Se configurar o servidor para cópias de segurança geograficamente redundantes, um novo servidor pode ser criado a partir da cópia de segurança desse servidor existente. Este novo servidor pode ser criado em qualquer região onde a Base de Dados Azure para MariaDB esteja disponível.  

1. Selecione o botão **De recurso** (+) no canto superior esquerdo do portal. Selecione **Base de Dados**  >  **Azure Databases para MariaDB**.

   :::image type="content" source="./media/howto-restore-server-portal/2_navigate-to-mariadb.png" alt-text="Navegue para a Base de Dados Azure para MariaDB.":::
 
2. Forneça a subscrição, o grupo de recursos e o nome do novo servidor. 

3. Selecione **Backup** como **fonte de dados**. Esta ação carrega uma redução que fornece uma lista de servidores que têm cópias de segurança geo redundantes ativadas.
   
   :::image type="content" source="./media/howto-restore-server-portal/3-geo-restore.png" alt-text="Selecione fonte de dados.":::
    
   > [!NOTE]
   > Quando um servidor é criado pela primeira vez, pode não estar imediatamente disponível para restauro geo. Pode levar algumas horas para que os metadados necessários sejam povoados.
   >

4. Selecione o **dropdown de backup.**
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore-backup.png" alt-text="Selecione o dropdown de reserva.":::

5. Selecione o servidor de origem para restaurar a partir de.
   
   :::image type="content" source="./media/howto-restore-server-portal/5-select-backup.png" alt-text="Selecione backup.":::

6. O servidor irá predefinir para valores para o número de **vCores** , Período de **Retenção de Cópia** de Segurança , **Opção de Redundância de Backup,** **Versão do Motor** e **credenciais de administração**. Selecione **Continuar**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/6-accept-backup.png" alt-text="Continue com reforços.":::

7. Preencha o resto do formulário com as suas preferências. Pode selecionar qualquer **localização.**

    Depois de selecionar a localização, pode selecionar o **servidor Configure** para atualizar a **Geração computacional** (se disponível na região escolhida), número de **vCores,** Período de **Retenção de Backup** e **Opção de Redundância de Backup**. A alteração **do nível de preços** (Final básico, geral ou otimizado da memória) ou o tamanho do **armazenamento** durante a restauração não é suportado.

   :::image type="content" source="./media/howto-restore-server-portal/7-create.png" alt-text="Preencher formulário."::: 

8. Selecione **Review + crie** para rever as suas seleções. 

9. Selecione **Criar** para aprovisionar o servidor. Esta operação poderá demorar alguns minutos.

O novo servidor criado pela geo restauro tem o mesmo nome de login de administrador do servidor e senha que era válido para o servidor existente no momento em que a restauração foi iniciada. A palavra-passe pode ser alterada a partir da página **geral** do novo servidor.

O novo servidor criado durante uma restauração não possui os pontos finais do serviço VNet que existiam no servidor original. Estas regras têm de ser configuradas separadamente para este novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as [cópias](concepts-backup.md) de segurança do serviço
- Saiba mais [sobre réplicas](concepts-read-replicas.md)
- Saiba mais sobre opções [de continuidade de negócios](concepts-business-continuity.md)