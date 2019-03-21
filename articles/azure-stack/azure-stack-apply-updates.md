---
title: Aplicar atualizações no Azure Stack | Documentos da Microsoft
description: Saiba como importar e instalar pacotes de atualização da Microsoft para um sistema integrado do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 9eca22f5a594bf3d61b1d68882c4853f4bfc499d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100756"
---
# <a name="apply-updates-in-azure-stack"></a>Aplicar atualizações no Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Pode utilizar o **atualizar** mosaico no portal de administração para aplicar os pacotes de atualização de OEM da Microsoft ou para o Azure Stack.

Se estiver a utilizar uma versão de sistemas integrados 1807 ou anterior, tem de transferir o pacote de atualização, importe os ficheiros de pacote para o Azure Stack e, em seguida, instale o pacote de atualização. Para obter instruções, consulte [atualização Azure Stack ao transferir o pacote](#update-azure-stack-by-downloading-the-package)

Atualizar estas instruções de trabalho com sistemas integrados do Azure Stack. Se estiver a utilizar o sistema de desenvolvimento do Azure Stack, tem de transferir o pacote de instalação para a versão atual. Para obter instruções, consulte [instalar o Kit de desenvolvimento do Azure Stack](./asdk/asdk-install.md)

## <a name="update-azure-stack"></a>Atualizar o Azure Stack

### <a name="select-and-apply-an-update-package"></a>Selecione e aplicar um pacote de atualização

1. Abra o portal de administração.

2. Selecione **Dashboard**. Selecione o **atualização** mosaico.

    ![Atualização de pilha do Azure disponível](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Tome nota da versão atual do seu Azure Stack. Pode atualizar para a próxima versão completa. Para o exemplo, se estiver a executar o Azure Stack 1811, a próxima foram lançadas as versões é 1901.

    ![Aplicar atualização de pilha do Azure](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. Selecione a próxima versão disponível na lista de atualizações. Pode selecionar **vista** na versão coluna anotações para abrir o tópico de notas de versão para a versão se gostaria de rever as alterações de versão.

5. Selecione a atualização agora. A atualização será iniciado.

### <a name="review-update-history"></a>Histórico de atualização de revisão

1. Abra o portal de administração.

2. Selecione **Dashboard**. Selecione o **atualização** mosaico.

3. Selecione **atualizar o histórico de**.

![Histórico de atualização de pilha do Azure](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Atualizar o Azure Stack ao transferir o pacote

Se estiver a utilizar uma versão de sistemas integrados 1807 ou anterior, tem de transferir o pacote de atualização, importe os ficheiros de pacote para o Azure Stack e, em seguida, instale o pacote de atualização.

## <a name="download-the-update-package"></a>Transferir o pacote de atualização

Quando um pacote de atualização de OEM da Microsoft ou para o Azure Stack está disponível, transferir o pacote para uma localização que esteja acessível a partir do Azure Stack e rever o conteúdo do pacote. Um pacote de atualização consiste, normalmente, os seguintes ficheiros:

- Extração automática `<PackageName>.exe` ficheiro. Este ficheiro contém a carga para a atualização, por exemplo a atualização cumulativa mais recente para o Windows Server.

- Correspondente `<PackageName>.bin` ficheiros. Estes ficheiros fornecem compactação para o payload que está associada a *PackageName*.exe ficheiro.

- A `Metadata.xml` ficheiro. Esse arquivo contém informações essenciais sobre a atualização, por exemplo o publicador, nome, pré-requisito, tamanho e URL do caminho de suporte.

> [!IMPORTANT]  
> Depois do pacote de atualização do Azure Stack 1901 é aplicado, o formato de empacotamento para pacakges de atualização do Azure Stack será movido de .exe, o .bin(s) e o formato. XML para um .zip(s) e o formato. XML. Os operadores do Azure Stack que ligou carimbos de data / não vão ser afetados. Os operadores do Azure Stack que estão ligado à Internet simplesmente irão importar o ficheiro ou ficheiros. XML e. zip ao utilizar o mesmo processo descrito abaixo.

## <a name="import-and-install-updates"></a>Importar e instalar atualizações

O procedimento seguinte mostra como importar e instalar pacotes de atualização no portal do administrador.

> [!IMPORTANT]  
> Recomendamos vivamente que notifique os utilizadores de quaisquer operações de manutenção e agendar a janelas de manutenção normal durante o horário não comercial tanto quanto possível. Operações de manutenção podem afetar as cargas de trabalho de utilizador e de operações do portal.

1. No portal do administrador, selecione **todos os serviços**. Em seguida, no **dados + armazenamento** categoria, selecione **contas de armazenamento**. (Ou, na caixa de filtro, comece a escrever **contas de armazenamento**e selecioná-lo.)

    ![Mostra onde encontrar as contas de armazenamento no portal](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Na caixa de filtro, escreva **atualizar**e selecione o **updateadminaccount** conta de armazenamento.

3. No armazenamento de detalhes da conta, em **serviços**, selecione **Blobs**.
 
    ![Mostra como obter para Blobs para a conta de armazenamento](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. Sob **serviço Blob**, selecione **+ contentor** para criar um contentor. Introduza um nome (por exemplo *atualização 1811*) e, em seguida, selecione **OK**.
 
     ![Mostra como adicionar um contentor na conta de armazenamento](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Depois do contentor é criado, clique no nome de contentor e, em seguida, clique em **carregar** para carregar os ficheiros de pacote para o contentor.
 
    ![Mostra como carregar os ficheiros do pacote](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Sob **carregar blob**, clique no ícone de pasta, procure .exe ficheiro o pacote de atualização e, em seguida, clique em **aberto** na janela do Explorador de ficheiros.
  
7. Sob **carregar blob**, clique em **carregar**.
  
    ![Mostra onde carregar cada ficheiro de pacote](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Repita os passos 6 e 7 para o *PackageName*. bin e arquivos de METADATA. Não importe o arquivo notice suplementares se incluído.
9. Quando terminar, pode rever as notificações (ícone de sino no canto superior direito do portal). As notificações devem indicar que concluiu o carregamento.
10. Navegue de volta para o atualizar mosaico no dashboard. O mosaico deve indicar que está disponível uma atualização. Clique no mosaico para rever o pacote de atualização adicionada recentemente.
11. Para instalar a atualização, selecione o pacote que está marcado como **pronto** e um pacote com o botão direito e selecione **atualizar agora**, ou clique nas **atualizar agora** ação perto da parte superior .
12. Ao clicar o pacote de atualização de instalação, pode ver o estado dos **detalhes de execução de atualização** área. A partir daqui, pode também clicar **transferir registos completos** para transferir os ficheiros de registo.
13. Quando a atualização estiver concluída, o mosaico de atualização mostra a versão atualizada do Azure Stack.

Pode eliminar manualmente as atualizações da conta de armazenamento depois de ter sido instaladas no Azure Stack. O Azure Stack periodicamente verifica a existência de pacotes de atualização de mais antigos e remove-los a partir do armazenamento. Poderá demorar Azure Stack duas semanas para remover os pacotes antigos.

## <a name="next-steps"></a>Passos Seguintes

- [Gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md)
- [O Azure Stack a política de manutenção](azure-stack-servicing-policy.md)
