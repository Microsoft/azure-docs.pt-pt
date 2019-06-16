---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66155862"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Atualizar o SharePoint 2010 para SharePoint 2013 e, em seguida, instalar o adaptador de StorSomple para SharePoint
> [!IMPORTANT]
> Todos os ficheiros que anteriormente foram movidos para o armazenamento externo através de RBS não estarão disponíveis até que a atualização estiver concluída e a funcionalidade RBS é ativada novamente. Para limitar o impacto no utilizador, execute qualquer atualização ou reinstalação durante uma janela de manutenção planeada.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Para atualizar o SharePoint 2010 para SharePoint 2013 e, em seguida, instalar o adaptador
1. No farm do SharePoint 2010, tenha em atenção o caminho de armazenamento de BLOBS para os BLOBs externalized e as bases de dados de conteúdo para o qual RBS está ativada. 
2. Instale e configure a nova farm do SharePoint 2013. 
3. Mova bases de dados, aplicativos e conjuntos de sites do farm do SharePoint 2010 para a nova farm do SharePoint 2013. Para obter instruções, aceda a [descrição geral do processo de atualização para o SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Instale o adaptador do StorSimple para SharePoint no farm de novo. Aceda a [instalar o adaptador do StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint) para obter os procedimentos.
5. Usando as informações que anotou no passo 1, ativar o RBS para o mesmo conjunto de bases de dados de conteúdo e fornecer o mesmo caminho de armazenamento de BLOBS que utilizou na instalação do SharePoint 2010. Aceda a [RBS configurar](#configure-rbs) para obter os procedimentos. Depois de concluir este passo, os ficheiros anteriormente externalized devem ser acessíveis a partir do novo farm. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Atualizar o adaptador do StorSimple para SharePoint
> [!IMPORTANT]
> Deve agendar esta atualização para ocorrer durante uma janela de manutenção planeada pelos seguintes motivos:
> 
> * Anteriormente conteúdo externalized não estarão disponível até que o adaptador é reinstalado.
> * Qualquer conteúdo carregado para o site depois de desinstalar a versão anterior do StorSimple Adapter para SharePoint, mas antes de instalar a nova versão, será armazenado na base de dados do conteúdo. Precisará mover esse conteúdo para o dispositivo StorSimple, depois de instalar o novo adaptador. Pode utilizar o Microsoft `RBS Migrate()` cmdlet do PowerShell incluído com o SharePoint para migrar o conteúdo. Para obter mais informações, consulte [migrar o conteúdo dentro ou fora do RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Para atualizar o adaptador do StorSimple para SharePoint
1. Desinstale a versão anterior do StorSimple Adapter para SharePoint.
   
   > [!NOTE]
   > Isto desativará RBS automaticamente nas bases de dados de conteúdo. No entanto, os BLOBs existentes permanecerão no dispositivo StorSimple. Uma vez RBS está desativada e os BLOBs não tiverem sido migrados para as bases de dados de conteúdo, todos os pedidos para os BLOBs irão falhar. 
   > 
   > 
2. Instale o novo adaptador do StorSimple para SharePoint. O novo adaptador reconhecerá automaticamente as bases de dados de conteúdos que foram anteriormente ativadas ou desativadas para RBS e irá utilizar as definições anteriores.

