---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184302"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Atualizar o SharePoint 2010 para SharePoint 2013 e, em seguida, instalar o Adaptador StorSomple para SharePoint
> [!IMPORTANT]
> Quaisquer ficheiros que foram previamente transferidos para armazenamento externo via RBS não estarão disponíveis até que a atualização esteja concluída e a funcionalidade RBS esteja novamente ativada. Para limitar o impacto do utilizador, efetue qualquer atualização ou reinstalação durante uma janela de manutenção planeada.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Para atualizar o SharePoint 2010 para o SharePoint 2013 e, em seguida, instalar o adaptador
1. Na quinta SharePoint 2010, note o caminho da loja BLOB para os BLOBs externos e as bases de dados de conteúdo para as quais o RBS está ativado. 
2. Instale e configuure a nova quinta SharePoint 2013. 
3. Mover bases de dados, aplicações e coleções de sites da quinta SharePoint 2010 para a nova quinta SharePoint 2013. Para obter instruções, vá ao [Resumo do processo de atualização para SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Instale o Adaptador StorSimple para SharePoint na nova quinta. Vá [instalar o adaptador StorSimple para o SharePoint](#install-the-storsimple-adapter-for-sharepoint) para obter procedimentos.
5. Utilizando as informações que notou no passo 1, ative o RBS para o mesmo conjunto de bases de dados de conteúdo e forneça o mesmo caminho de loja BLOB que foi utilizado na instalação do SharePoint 2010. Vá ao [Configure RBS](#configure-rbs) para procedimentos. Depois de completar este passo, os ficheiros previamente externos devem estar acessíveis a partir da nova quinta. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Atualize o Adaptador StorSimple para SharePoint
> [!IMPORTANT]
> Deverá agendar esta atualização para ocorrer durante uma janela de manutenção planeada pelas seguintes razões:
> 
> * Os conteúdos previamente externos não estarão disponíveis até que o adaptador seja reinstalado.
> * Qualquer conteúdo enviado para o site depois de desinstalar a versão anterior do Adaptador StorSimple para o SharePoint, mas antes de instalar a nova versão, será armazenado na base de dados de conteúdos. Terá de mover esse conteúdo para o dispositivo StorSimple depois de instalar o novo adaptador. Pode utilizar o `RBS Migrate()` cmdlet Microsoft PowerShell incluído no SharePoint para migrar o conteúdo. Para obter mais informações, consulte [o conteúdo da Migração dentro ou fora do RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Para atualizar o Adaptador StorSimple para SharePoint
1. Desinstale a versão anterior do Adaptador StorSimple para o SharePoint.
   
   > [!NOTE]
   > Isto irá desativar automaticamente o RBS nas bases de dados de conteúdos. No entanto, os BLOBs existentes permanecerão no dispositivo StorSimple. Uma vez que o RBS está desativado e os BLOBs não foram migrados de volta para as bases de dados de conteúdos, quaisquer pedidos para esses BLOBs falharão. 
   > 
   > 
2. Instale o novo Adaptador StorSimple para o SharePoint. O novo adaptador reconhecerá automaticamente as bases de dados de conteúdo que foram previamente ativadas ou desativadas para o RBS e utilizará as definições anteriores.

