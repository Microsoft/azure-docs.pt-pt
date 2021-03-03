---
title: Configure apoio operacional para Azure Blobs
description: Saiba como configurar e gerir o backup operacional para Azure Blobs (na pré-visualização)
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7fe302de0e93575e7bb62f7c5cdefd7acc0720c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746788"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Configure o backup operacional para Azure Blobs (em pré-visualização)

O Azure Backup permite-lhe configurar facilmente o backup operacional para proteger as bolhas de bloqueio nas suas contas de armazenamento. Este artigo explica como configurar a cópia de segurança operacional em uma ou mais contas de armazenamento utilizando o portal Azure. O artigo aborda o seguinte:

- Coisas a saber antes de começar
- Criando um cofre de reserva
- Concessão de permissões ao Cofre de Reserva nas contas de armazenamento a proteger
- Criação de uma política de backup
- Configurar o backup operacional em uma ou mais contas de armazenamento
- Efeitos nas contas de armazenamento de backup

## <a name="before-you-start"></a>Antes de começar

- A cópia de segurança operacional das bolhas é uma solução de backup local que mantém os dados durante uma determinada duração na própria conta de armazenamento de origem. Esta solução não mantém uma cópia adicional de dados no cofre.
- Esta solução permite-lhe reter os seus dados para restauro até 360 dias. No entanto, as longas durações de retenção podem conduzir a um período mais longo durante a operação de restauro.
- A solução pode ser usada para efetuar restauros apenas na conta de armazenamento de origem e pode resultar em sobreescrita de dados.
- Se eliminar um recipiente da conta de armazenamento chamando a operação do Recipiente delete, esse recipiente não pode ser restaurado com uma operação de restauro. Em vez de eliminar um recipiente inteiro, elimine as bolhas individuais se desejar restaurá-las mais tarde. Além disso, a Microsoft recomenda que se permita eliminar suavemente os recipientes, além de cópias de segurança operacionais, para proteger contra a eliminação acidental dos contentores.
- Consulte a [matriz de suporte](blob-backup-support-matrix.md) para saber mais sobre os cenários, limitações e disponibilidade suportados.

## <a name="create-a-backup-vault"></a>Criar um cofre de reserva

Um [cofre de backup](backup-vault-overview.md) é uma entidade de gestão que armazena pontos de recuperação criados ao longo do tempo e fornece uma interface para realizar operações relacionadas com backup. Estas incluem criar cópias de segurança a pedido, realizar restauros e criar políticas de cópia de segurança. Embora o backup operacional das bolhas seja uma cópia de segurança local e não "armazene" dados no cofre, o cofre é necessário para várias operações de gestão.

>[!NOTE]
>O cofre de backup é um novo recurso que é usado para apoiar novas cargas de trabalho suportadas e é diferente do cofre já existente dos Serviços de Recuperação.

Para obter instruções sobre como criar um cofre de reserva, consulte a [documentação](backup-vault-overview.md#create-a-backup-vault)do cofre de reserva .

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Conceder permissões ao cofre de reserva em contas de armazenamento

A cópia de segurança operacional também protege a conta de armazenamento (que contém as bolhas a proteger) contra quaisquer supressões acidentais aplicando um bloqueio de eliminação de propriedade de cópia de segurança. Isto requer que o cofre de reserva tenha certas permissões nas contas de armazenamento que precisam de ser protegidas. Para conveniência de utilização, estas permissões foram consolidadas sob a função de Contribuinte de Backup de Conta de Armazenamento. Siga as instruções abaixo para as contas de armazenamento que precisam de ser protegidas:

1. Na conta de armazenamento a proteger, navegue para o **separador Controlo de Acesso (IAM)** no painel de navegação esquerdo.
1. Selecione **Atribuir atribuições de funções** para atribuir a função necessária.

    ![Adicionar atribuições de funções](./media/blob-backup-configure-manage/add-role-assignments.png)

1. No painel de atribuição de funções Add:

    1. Under **Role**, escolha **o contribuinte de backup da conta de armazenamento.**
    1. No **Acesso a Atribuir acesso a**, escolha **Utilizador, grupo ou principal de serviço.**
    1. Digite o **nome do cofre de reserva** que pretende proteger as bolhas nesta conta de armazenamento e selecione o mesmo a partir dos resultados da pesquisa.
    1. Uma vez feito, **selecione Guardar**.

        ![Opções de atribuição de funções](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Deixe até 10 minutos para que a tarefa de função entre em vigor.

## <a name="create-a-backup-policy"></a>Criar uma política de cópias de segurança

Uma política de backup normalmente rege a retenção e o horário das suas cópias de segurança. Uma vez que o backup operacional para blobs é de natureza contínua, você não precisa de um horário para fazer backups. A política é essencialmente necessária para especificar o período de retenção. Pode utilizar e reutilizar a política de backup para configurar a cópia de segurança para várias contas de armazenamento num cofre.

Aqui estão os passos para criar uma política de backup para o backup operacional das suas bolhas:

1. No seu cofre de backup, navegue para **as políticas de Backup** e selecione **+Adicionar** para começar a criar uma política de backup.

    ![Políticas de backup](./media/blob-backup-configure-manage/backup-policies.png)

1. No **separador Basics,** forneça um nome para a sua política de backup e selecione **Azure Blobs** como o tipo de fonte de dados. Também pode ver os detalhes do seu cofre selecionado.

    ![Criar política de backup](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Embora veja a **redundância** de armazenamento de reserva do cofre, a redundância não se aplica à cópia de segurança operacional das bolhas, uma vez que a cópia de segurança é de natureza local e não há dados armazenados no cofre de reserva. O cofre de reserva aqui é a entidade de gestão para ajudá-lo a gerir a proteção de blobs de blocos nas suas contas de armazenamento.

1. O separador **de política de backup** é onde especifica os detalhes da retenção. Verá que já existe uma regra de retenção chamada **Default** com um período de retenção de 30 dias. Se pretender editar a duração da retenção, utilize o ícone **da regra de retenção de edição** para editar e especifique a duração para a qual pretende que os dados sejam retidos. Pode especificar a retenção até 360 dias.

    ![Separador de política de backup](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >Restaurar durante longas durações pode levar a restaurar as operações demorando mais tempo a ser concluídas. Além disso, o tempo que leva para restaurar um conjunto de dados baseia-se no número de operações de escrita e eliminação efetuadas durante o período de restauração. Por exemplo, uma conta com um milhão de objetos com 3.000 objetos adicionados por dia e 1.000 objetos apagados por dia exigirá aproximadamente duas horas para restaurar um ponto de 30 dias no passado. Um período de retenção e restauro superior a 90 dias no passado não seria recomendado para uma conta com esta taxa de variação.

1. No **Painel de Avaliação + criar** painel, verificar todos os detalhes da política e selecione **Criar** uma vez feito para terminar a criação da política. Uma notificação confirmará uma vez que a política de backup foi criada e está pronta para ser usada.

    ![Rever e criar política](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança

A cópia de segurança das bolhas está configurada ao nível da conta de armazenamento. Assim, todas as bolhas na conta de armazenamento estão protegidas com reforço operacional.

Para começar a configurar a cópia de segurança:

1. Procure **o Backup Center** na barra de pesquisa.
1. Navegue para **visão geral**  ->  **+Backup**.

    ![Visão geral do Backup Center](./media/blob-backup-configure-manage/backup-center-overview.png)

1. No **separador Iniciar: Configurar o separador Backup,** escolha **Azure Blobs (Azure Storage)** como o tipo de Fonte de Dados.

    ![Iniciar: Separador de backup de configuração](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. No **separador Basics, especifique** **a Azure Blobs (Azure Storage)** como o tipo **de Fonte de Dados** e selecione o cofre de reserva ao qual pretende associar as suas contas de armazenamento. Pode ver detalhes do cofre selecionado no painel.

    ![Separador básico](./media/blob-backup-configure-manage/basics-tab.png)

1. Em seguida, selecione a política de backup que pretende utilizar para especificar a retenção. Pode ver os detalhes da política selecionada na parte inferior do ecrã. A coluna de armazenamento de dados operacionais mostra a retenção definida na política. "Operacional" significa que os dados são mantidos localmente na própria conta de armazenamento de origem.

    ![Escolha a política de backup](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Também pode criar uma nova política de backup. Para isso, **selecione Criar novos** e siga os passos abaixo:

    1. Forneça um nome para a política que pretende criar. Certifique-se de que as outras caixas exibem o tipo de fonte de dados correto e o nome do Cofre.
    1. No separador **política de Backup,** selecione o ícone da regra de retenção de edição para editar e especifique a duração para a qual deseja que os dados sejam retidos. Pode especificar a retenção até 360 dias. Restaurar durante longas durações pode levar a restaurar as operações demorando mais tempo a ser concluídas.

        ![Criar nova política de backup](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Uma vez feito, selecione **Rever + criar** para criar a política de backup.

1. Em seguida, é obrigado a escolher as contas de armazenamento para as quais pretende configurar a proteção das bolhas. Pode escolher várias contas de armazenamento de uma só vez e escolher **Select**.

    No entanto, certifique-se de que o cofre que escolheu tem as permissões necessárias para configurar a cópia de segurança nas contas de armazenamento, conforme descrito acima nas [permissões grant para o cofre de reserva nas contas de armazenamento.](#grant-permissions-to-the-backup-vault-on-storage-accounts)

    ![Selecione recursos para fazer back-up](./media/blob-backup-configure-manage/select-resources.png)

    Verificações de backup se o cofre tiver permissões suficientes para permitir a configuração da cópia de segurança nas contas de armazenamento selecionadas.

    ![Backup valida permissões](./media/blob-backup-configure-manage/validate-permissions.png)

    Se a validação resultar em erros (como com uma das contas de armazenamento na imagem), vá às contas de armazenamento selecionadas e atribua as funções adequadas, conforme detalhado [aqui,](#grant-permissions-to-the-backup-vault-on-storage-accounts)e selecione **Revalidate**. A nova atribuição de funções pode demorar até 10 minutos a produzir efeitos.

1. Uma vez que a validação tenha sucesso para todas as contas de armazenamento selecionadas, continue a **rever e configuure-se** para configurar a cópia de segurança. Verá notificações informando-o sobre o estado de proteção configurante e a sua conclusão.

## <a name="effects-on-backed-up-storage-accounts"></a>Efeitos nas contas de armazenamento apoiadas

Uma vez configurada a cópia de segurança, as alterações que ocorrem nas bolhas de blocos nas contas de armazenamento são rastreadas e os dados são retidos de acordo com a política de backup. Notará as seguintes alterações nas contas de armazenamento para as quais a cópia de segurança está configurada:

- As seguintes capacidades estão ativadas na conta de armazenamento. Estes podem ser vistos no **separador Proteção** de Dados da conta de armazenamento.
  - Ponto no tempo restaurado para contentores: Com retenção conforme especificado na política de backup
  - Excluir suave para bolhas: Com retenção conforme especificado na política de backup +5 dias
  - Versão para bolhas
  - Feed de mudança de bolha

  Se a conta de armazenamento configurada para cópia de segurança já tivesse  **a Point a tempo de ser restaurada para contentores** ou **eliminação suave para bolhas** ativadas (antes de a cópia de segurança ser configurada), o Backup garante que a retenção está pelo menos como definida na política de backup. Portanto, para cada imóvel:

  - Se a retenção na política de backup for maior do que a retenção originalmente presente na conta de armazenamento: A retenção na conta de armazenamento é modificada de acordo com a política de backup
  - Se a retenção na política de backup for inferior à retenção originalmente presente na conta de armazenamento: A retenção na conta de armazenamento permanece inalterada na duração inicialmente definida.

  ![Separador de proteção de dados](./media/blob-backup-configure-manage/data-protection.png)

- Um **bloqueio de eliminação** é aplicado por cópia de segurança na conta de armazenamento protegida. O cadeado destina-se a salvaguardar contra casos de supressão acidental da conta de armazenamento. Isto pode ser visto em **Fechaduras de Conta de Armazenamento.**  >  

    ![Bloqueios de eliminação](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Gerir backup operacional

Pode utilizar o Backup Center como o seu único painel de vidro para gerir todos os seus backups. No que diz respeito à cópia de segurança operacional da Azure Blobs, pode utilizar o Backup Center para executar o seguinte:

- Como vimos acima, pode usá-lo para criar cofres e políticas de reserva. Também pode ver todos os cofres e políticas sob as subscrições selecionadas.
- O Backup Center dá-lhe uma forma fácil de monitorizar o estado de proteção das contas de armazenamento protegidas, bem como contas de armazenamento para as quais a cópia de segurança não está atualmente configurada.
- Pode configurar a cópia de segurança para quaisquer contas de armazenamento utilizando o botão **+Backup.**
- Pode iniciar restauros utilizando o botão **Restaurar** e restaurar a via utilizando **trabalhos de backup**. Para obter mais informações sobre a realização de restauros, consulte [Restaurar as manchas de Azure.](blob-backup-support-matrix.md)
- Analise o seu uso de cópia de segurança utilizando relatórios de backup.

    ![Centro de Cópias de Segurança](./media/blob-backup-configure-manage/backup-center.png)

Para obter mais informações, consulte [a visão geral do Backup Center (Preview)](backup-center-overview.md).

## <a name="next-steps"></a>Passos seguintes

- [Restaurar as bolhas de Azure](blob-backup-support-matrix.md)
