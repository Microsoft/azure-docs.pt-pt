---
title: Restaurar as bolhas de Azure
description: Saiba como restaurar as bolhas de Azure (em pré-visualização).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746498"
---
# <a name="restore-azure-blobs-in-preview"></a>Restaurar Azure Blobs (na pré-visualização)

As bolhas de blocos nas contas de armazenamento com cópia de segurança operacional configuradas podem ser restauradas a qualquer ponto do tempo dentro do intervalo de retenção. Além disso, pode estender as suas restaurações a todas as bolhas de bloqueio na conta de armazenamento ou a um subconjunto de bolhas.

## <a name="before-you-start"></a>Antes de começar

- As bolhas serão restauradas na mesma conta de armazenamento. Então, as bolhas que sofreram alterações desde o tempo em que estás a restaurar serão substituídas.
- Apenas as bolhas de bloco numa conta de armazenamento v2 de uso geral padrão podem ser restauradas como parte de uma operação de restauro. Bolhas de apêndice, bolhas de página e bolhas de bloco premium não são restauradas.
- Enquanto um trabalho de restauro está em andamento, as bolhas no armazenamento não podem ser lidas ou escritas.
- Uma bolha com um arrendamento ativo não pode ser restaurada. Se uma bolha com um arrendamento ativo for incluída na gama de bolhas para restaurar, a operação de restauro falhará automaticamente. Quebre quaisquer locações ativas antes de iniciar a operação de restauro.
- As imagens não são criadas ou eliminadas como parte de uma operação de restauro. Apenas a bolha da base é restaurada ao seu estado anterior.
- Se eliminar um recipiente da conta de armazenamento chamando a operação **do Recipiente delete,** esse recipiente não pode ser restaurado com uma operação de restauro. Em vez de eliminar um recipiente inteiro, elimine as bolhas individuais se desejar restaurá-las mais tarde. Além disso, a Microsoft recomenda que se permita eliminar suavemente os recipientes, além de uma cópia de segurança operacional para proteger contra a eliminação acidental de contentores.
- Consulte a [matriz de suporte](blob-backup-support-matrix.md) para todas as limitações e cenários suportados.

## <a name="restore-blobs"></a>Restaurar bolhas

Pode iniciar uma restauração através do Centro de Reserva.

1. No Back Center, vá ao **Restore** na barra superior.

    ![Restaurar no Centro de Apoio](./media/blob-restore/backup-center-restore.png)

1. No **separador Iniciar Restaurar,** escolha **Azure Blobs (Azure Storage)** como o tipo de fonte de dados e selecione a **Instância de Backup** que pretende restaurar. A parte de reserva aqui é a conta de armazenamento que contém as bolhas que você deseja restaurar.

     ![Selecione a instância de backup](./media/blob-restore/select-backup-instance.png)

1. No **separador ponto de recuperação Select,** escolha a data e a hora a partir da qual pretende restaurar os seus dados. Também pode utilizar o slider para escolher o ponto a tempo de ser restaurado. A bolha de informação ao lado da data mostra a duração válida a partir da qual pode restaurar os seus dados. A cópia de segurança operacional para as bolhas que são de backup contínua dá-lhe controlo granular sobre pontos para recuperar dados.

    >[!NOTE]
    > O tempo aqui retratado é a sua hora local.

    ![Data e hora para restauro](./media/blob-restore/date-and-time.png)

1. No separador **parâmetros 'Restaurar'** escolha se pretende restaurar todas as bolhas na conta de armazenamento, recipientes específicos ou um subconjunto de bolhas utilizando correspondência de prefixo. Ao utilizar a correspondência de prefixo, pode especificar até 10 gamas de prefixos ou caminhos de ficheiros. Mais detalhes sobre a utilização do prefixo match [aqui](#use-prefix-match-for-restoring-blobs).

    ![Restaurar parâmetros](./media/blob-restore/restore-parameters.png)

    Escolha uma destas opções:

    - **Restaurar todas as bolhas na conta de armazenamento**: Utilizando esta opção, restaura todas as bolhas de bloco na conta de armazenamento, rolando-as de volta para o ponto selecionado no tempo. As contas de armazenamento que contenham grandes quantidades de dados ou que testemunhem uma alta agitação podem demorar mais tempo a ser restauradas.

    - **Procurar e restaurar recipientes selecionados**: A utilização desta opção permite-lhe navegar e selecionar até 10 recipientes para restaurar. Deve ter permissões suficientes para visualizar os contentores na conta de armazenamento, caso contrário poderá não conseguir ver o conteúdo da conta de armazenamento.

    - **Selecione bolhas para restaurar usando a correspondência de prefixo**: Esta opção permite restaurar um subconjunto de bolhas utilizando uma correspondência de prefixo. Pode especificar até 10 gamas lexicográficas de bolhas dentro de um único recipiente ou em vários recipientes para devolver essas bolhas ao seu estado anterior num dado momento. Eis algumas coisas a ter em mente:

        - Pode utilizar um corte para a frente (/) para delinear o nome do recipiente a partir do prefixo blob
        - O início da gama especificada é inclusivo, no entanto a gama especificada é exclusiva.

    Para obter mais informações sobre a utilização de prefixos para restaurar as gamas blob, consulte [esta secção](#use-prefix-match-for-restoring-blobs).

1. Assim que terminar de especificar quais as bolhas para restaurar, continue no **separador 'Rever + restaurar'** e selecione **Restaurar** para iniciar a restauração.

1. **Restauração da faixa**: Utilize a vista **"Trabalhos de Reserva"** para acompanhar os detalhes e o estado das restaurações. Para isso, navegue para **backup center**  >  **jobs**. O estado mostrará **em curso** enquanto a restauração estiver a ser executada.

    ![Separador de empregos de reserva](./media/blob-restore/backup-jobs.png)

    Quando a operação de restauro estiver concluída com sucesso, o estado mudará para **Concluído**. Assim que a restauração estiver concluída com sucesso, poderá ler e escrever bolhas na conta de armazenamento novamente.

## <a name="additional-topics"></a>Tópicos adicionais

### <a name="use-prefix-match-for-restoring-blobs"></a>Use o prefixo match para restaurar as bolhas

Considere o exemplo seguinte:

![Restaurar com prefixo match](./media/blob-restore/prefix-match.png)

A operação de restauro mostrada na imagem realiza as seguintes ações:

- Restaura o conteúdo completo do *contentor1*.
- Restaura bolhas na gama lexicográfica *blob1* através da *bolha5* em *recipiente2*. Esta gama restaura bolhas com nomes como *blob1,* *blob11,* *blob100,* *blob2*, e assim por diante. Como o fim da gama é exclusivo, restaura bolhas cujos nomes começam com *blob4,* mas não restaura blobs cujos nomes começam com *blob5*.
- Restaura todas as bolhas em *recipiente3* e *contentor4*. Como o fim da gama é exclusivo, esta gama não restaura *o contentor5.*

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do backup operacional para Azure Blobs (em pré-visualização)](blob-backup-overview.md)
