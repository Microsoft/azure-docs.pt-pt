---
title: Backup e restauração-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como restaurar um servidor no banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: fb13e4f062976e39c3cec607001e6982db228873
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765635"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Como fazer backup e restaurar um servidor no banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure

## <a name="backup-happens-automatically"></a>O backup ocorre automaticamente
O backup do banco de dados do Azure para servidores PostgreSQL é feito periodicamente para habilitar os recursos de restauração. Usando esse recurso, você pode restaurar o servidor e todos os seus bancos de dados para um ponto anterior no tempo, em um novo servidor.

## <a name="set-backup-configuration"></a>Definir configuração de backup

Você faz a escolha entre configurar o servidor para backups com redundância local ou backups com redundância geográfica na criação do servidor, na janela **tipo de preço** .

> [!NOTE]
> Depois que um servidor é criado, o tipo de redundância que ele tem, geograficamente redundante versus redundância local, não pode ser alternado.
>

Ao criar um servidor por meio do portal do Azure, a janela **tipo de preço** é onde você seleciona backups com redundância **local** ou com **redundância geográfica** para seu servidor. Essa janela também é onde você seleciona o **período de retenção de backup** – por quanto tempo (em número de dias) você deseja que os backups de servidor sejam armazenados.

   ![Tipo de preço – escolha a redundância de backup](./media/howto-restore-server-portal/pricing-tier.png)

Para obter mais informações sobre como definir esses valores durante a criação, consulte o guia de [início rápido do servidor do banco de dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md).

O período de retenção de backup de um servidor pode ser alterado por meio das seguintes etapas:
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione o servidor da Base de Dados do Azure para PostgreSQL. Essa ação abre a página **visão geral** .
3. Selecione **tipo de preço** no menu, em **configurações**. Usando o controle deslizante, você pode alterar o **período de retenção de backup** para sua preferência entre 7 e 35 dias.
Na captura de tela abaixo, foi aumentado para 34 dias.
![período de retenção de backup aumentado](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Clique em **OK** para confirmar a alteração.

O período de retenção de backup controla o quanto o tempo uma restauração pontual pode ser recuperada, pois ela é baseada em backups disponíveis. A restauração pontual é descrita mais detalhadamente na seção a seguir. 

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
O banco de dados do Azure para PostgreSQL permite que você restaure o servidor de volta para um ponto no tempo e para uma nova cópia do servidor. Você pode usar esse novo servidor para recuperar seus dados ou fazer com que os aplicativos cliente apontem para esse novo servidor.

Por exemplo, se uma tabela foi acidentalmente descartada ao meio-dia de hoje, você poderia restaurar o tempo logo antes do meio-dia e recuperar a tabela e os dados ausentes dessa nova cópia do servidor. A restauração pontual está no nível do servidor, não no nível do banco de dados.

As etapas a seguir restauram o servidor de exemplo para um ponto no tempo:
1. Na portal do Azure, selecione seu banco de dados do Azure para o servidor PostgreSQL. 

2. Na barra de ferramentas da página **visão geral** do servidor, selecione **restaurar**.

   ![Banco de dados do Azure para PostgreSQL-visão geral – botão restaurar](./media/howto-restore-server-portal/2-server.png)

3. Preencha o formulário de restauração com as informações necessárias:

   ![Banco de dados do Azure para PostgreSQL-informações de restauração](./media/howto-restore-server-portal/3-restore.png)
   - **Ponto de restauração**: selecione o ponto no tempo no qual você deseja restaurar.
   - **Servidor de destino**: forneça um nome para o novo servidor.
   - **Local**: não é possível selecionar a região. Por padrão, é o mesmo que o servidor de origem.
   - **Tipo de preço**: não é possível alterar esses parâmetros ao fazer uma restauração pontual. É igual ao servidor de origem. 

4. Clique em **OK** para restaurar o servidor para restaurar para um ponto no tempo. 

5. Quando a restauração for concluída, localize o novo servidor que é criado para verificar se os dados foram restaurados conforme o esperado.

O novo servidor criado pela restauração pontual tem o mesmo nome de logon do administrador do servidor e a senha que era válida para o servidor existente no momento escolhido. Você pode alterar a senha na página de **visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem as regras de firewall ou os pontos de extremidade de serviço de VNet que existiam no servidor original. Essas regras precisam ser configuradas separadamente para esse novo servidor.


## <a name="geo-restore"></a>Restauração geográfica

Se você configurou o servidor para backups com redundância geográfica, um novo servidor pode ser criado a partir do backup desse servidor existente. Esse novo servidor pode ser criado em qualquer região em que o banco de dados do Azure para PostgreSQL esteja disponível.  

1. Selecione o botão (+) **Criar um recurso**, no canto superior esquerdo do portal. Selecione **Bases de Dados** > **Base de Dados do Azure para PostgreSQL**.

   ![Opção “Base de Dados do Azure para o PostgreSQL”](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. Na lista suspensa **selecionar origem** do formulário, escolha **backup**. Esta ação carrega uma lista de servidores que têm backups com redundância geográfica habilitada. Selecione um desses backups para ser a origem do novo servidor.
   ![selecionar fonte: backup e lista de backups com redundância geográfica](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Quando um servidor é criado pela primeira vez, ele pode não estar imediatamente disponível para a restauração geográfica. Pode levar algumas horas para que os metadados necessários sejam preenchidos.
   >

3. Preencha o restante do formulário com suas preferências. Você pode selecionar qualquer **local**. Depois de selecionar o local, você pode selecionar **tipo de preço**. Por padrão, os parâmetros do servidor existente do qual você está restaurando são exibidos. Você pode clicar em **OK** sem fazer nenhuma alteração para herdar essas configurações. Ou você pode alterar **a geração de computação** (se disponível na região que você escolheu), o número de **VCores**, o período de **retenção de backup**e a opção de **redundância de backup**. A alteração do **tipo de preço** (básico, uso geral ou otimizado para memória) ou o tamanho do **armazenamento** durante a restauração não tem suporte.


O novo servidor criado pela restauração geográfica tem o mesmo nome de logon do administrador do servidor e a senha que era válida para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **visão geral** do novo servidor.

O novo servidor criado durante uma restauração não tem as regras de firewall ou os pontos de extremidade de serviço de VNet que existiam no servidor original. Essas regras precisam ser configuradas separadamente para esse novo servidor.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [backups](concepts-backup.md)do serviço.
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md) .
