---
title: Como restaurar um servidor na base de dados do Azure para PostgreSQL
description: Este artigo descreve como restaurar um servidor na base de dados do Azure para PostgreSQL com o portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2018
ms.openlocfilehash: a4f9fee0c405c893d76d044278c80e7b31823fce
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881350"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Como criar cópias de segurança e restaurar um servidor na base de dados do Azure para PostgreSQL com o portal do Azure

## <a name="backup-happens-automatically"></a>Cópia de segurança ocorre automaticamente
Base de dados do Azure para servidores PostgreSQL são uma cópia de segurança periodicamente para ativar funcionalidades de restauro. Ao utilizar esta funcionalidade pode restaurar o servidor e todas as suas bases de dados para um anterior ponto anterior no tempo, num servidor novo.

## <a name="set-backup-configuration"></a>Configuração de conjunto de cópia de segurança

Fazer a escolha entre configurar seu servidor para cópias de segurança localmente redundantes ou cópias de segurança georredundante durante a criação do servidor, no **escalão de preço** janela.

> [!NOTE]
> Depois de criar um servidor, o tipo de redundância tem, vs geograficamente redundantes localmente redundantes, não pode ser mudado.
>

Ao criar um servidor através do portal do Azure, o **escalão de preço** janela é onde seleciona um **localmente redundante** ou **geograficamente redundante** as cópias de segurança o servidor. Esta janela também é onde pode selecionar o **período de retenção de cópia de segurança** - o intervalo de tempo (num número de dias) que pretende que as cópias de segurança do servidor armazenadas para.

   ![Preços - escolher redundância de cópia de segurança](./media/howto-restore-server-portal/pricing-tier.png)

Para obter mais informações sobre como definir esses valores durante a criação, consulte a [base de dados do Azure para o guia de introdução do PostgreSQL server](quickstart-create-server-database-portal.md).

O período de retenção de cópia de segurança de um servidor de pode ser alterado os seguintes passos:
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione o servidor da Base de Dados do Azure para PostgreSQL. Esta ação abre o **descrição geral** página.
3. Selecione **escalão de preço** no menu, sob **definições**. No controlo de deslize pode alterar o **período de retenção de cópia de segurança** para sua preferência entre 7 e 35 dias.
Na captura de ecrã abaixo, foi aumentado para dias 34.
![Aumento do período de retenção de cópia de segurança](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Clique em **OK** para confirmar a alteração.

O período de retenção de cópia de segurança controla até que ponto no tempo que pode ser obtido um restauro de ponto no tempo, uma vez que é baseado em cópias de segurança disponíveis. Restauro de ponto no tempo é descrito mais detalhadamente na secção seguinte. 

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
Base de dados do Azure para PostgreSQL permite-lhe restaurar o servidor para um ponto anterior no tempo e em para uma nova cópia do servidor. Pode utilizar este servidor novo para recuperar os dados ou manter seus aplicativos de cliente que aponte para este servidor novo.

Por exemplo, se acidentalmente uma tabela tiver sido removido ao meio-dia hoje em dia, poderia restaurar para o tempo, pouco antes do meio-dia e obter a tabela em falta e os dados a partir dessa nova cópia do servidor. Restauro de ponto no tempo está no servidor de nível, não ao nível da base de dados.

Os passos seguintes restauram o servidor de exemplo para um ponto anterior no tempo:
1. No portal do Azure, selecione a base de dados do Azure para o servidor PostgreSQL. 

2. Na barra de ferramentas do servidor **descrição geral** página, selecione **restaurar**.

   ![Base de dados do Azure para PostgreSQL - descrição geral - restauro botão](./media/howto-restore-server-portal/2-server.png)

3. Preencha o formulário de restauro com as informações necessárias:

   ![Base de dados do Azure para PostgreSQL - informações de restauro](./media/howto-restore-server-portal/3-restore.png)
  - **Ponto de restauro**: Selecione o ponto anterior no tempo que pretende restaurar para.
  - **Servidor de destino**: Forneça um nome para o novo servidor.
  - **Localização**: Não é possível selecionar a região. Por predefinição é igual ao servidor de origem.
  - **Escalão de preço**: Não é possível alterar estes parâmetros ao efetuar um restauro de ponto no tempo. É igual ao servidor de origem. 

4. Clique em **OK** para restaurar o servidor para restaurar para um ponto anterior no tempo. 

5. Depois do restauro ser concluído, localize o novo servidor, que é criado para verificar que os dados foram restaurados conforme esperado.

>[!Note]
>O novo servidor criado pelo restauro para ponto no tempo tem o mesmo nome de início de sessão de administrador do servidor e palavra-passe que foi válido para o servidor existente ao ponto no tempo escolhido. Pode alterar a palavra-passe a partir do novo servidor **descrição geral** página.

## <a name="geo-restore"></a>Restauro geográfico
Se tiver configurado o seu servidor para cópias de segurança georredundante, um novo servidor de pode ser criado da cópia de segurança desse servidor existente. Este novo servidor de pode ser criado em qualquer região que a base de dados do Azure para PostgreSQL está disponível.  

1. Selecione o botão (+) **Criar um recurso**, no canto superior esquerdo do portal. Selecione **Bases de Dados** > **Base de Dados do Azure para PostgreSQL**.

   ![Opção “Base de Dados do Azure para o PostgreSQL”](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. O formato **selecionar origem** menu pendente, escolha **cópia de segurança**. Esta ação carrega uma lista de servidores que tenham geo redundante as cópias de segurança ativadas. Selecione um destas cópias de segurança para ser a origem do seu novo servidor.
   ![Selecione a origem: Cópia de segurança e a lista de cópias de segurança com redundância geográfica](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Quando é criado um servidor pode não estar imediatamente disponível para o restauro geográfico. Poderá demorar algumas horas para os metadados necessários ser preenchido.
   >

3. Preencha o resto do formulário com suas preferências. Pode selecionar qualquer **localização**. Depois de selecionar a localização, pode selecionar **escalão de preço**. Por predefinição, são apresentados os parâmetros para o servidor existente que está a restaurar. Pode clicar em **OK** sem fazer quaisquer alterações para herdar as definições. Ou pode alterar **geração de computação** (se disponível na região que escolheu), o número de **vCores**, **período de retenção de cópia de segurança**, e **cópia de segurança Opção de redundância**. A alteração **escalão de preço** (básico, fins gerais ou com otimização de memória) ou **armazenamento** tamanho durante o restauro não é suportado.

>[!Note]
>O novo servidor criado pelo Restauro geográfico tem o mesmo nome de início de sessão de administrador do servidor e palavra-passe que foi válido para o servidor existente no momento que do restauro foi iniciado. A palavra-passe pode ser alterada a partir do novo servidor **descrição geral** página.


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o serviço [cópias de segurança](concepts-backup.md).
- Saiba mais sobre [continuidade do negócio](concepts-business-continuity.md) opções.
