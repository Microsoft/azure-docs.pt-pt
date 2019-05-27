---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155871"
---
> [!NOTE]
> Ao fazer alterações para o adaptador do StorSimple para configuração de RBS do SharePoint, deve fazer logon com uma conta de utilizador que pertence ao grupo Admins do domínio. Além disso, precisa acessar a página de configuração a partir de um browser em execução no mesmo anfitrião Administração Central.
> 
> 

#### <a name="to-configure-rbs"></a>Para configurar o RBS
1. Abra a página de Administração Central do SharePoint e navegue até **definições do sistema**. 
2. Na **Azure StorSimple** secção, clique em **configurar o adaptador do StorSimple**.
   
    ![Configure o adaptador do StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Sobre o **configurar o adaptador do StorSimple** página:
   
   1. Certifique-se de que o **ativar edição caminho** caixa de verificação está selecionada.
   2. Na caixa de texto, escreva o caminho de convenção de Nomenclatura Universal (UNC) do armazenamento de BLOBS.
      
      > [!NOTE]
      > O volume de armazenamento de BLOBS tem de estar alojado num volume iSCSI configurado no dispositivo StorSimple.

   3. Clique nas **ativar** botão abaixo de cada uma das bases de dados conteúdos que pretende configurar para armazenamento remoto.
      
      > [!NOTE]
      > O armazenamento de BLOBS tem de ser partilhada e pode ser acedida por todos os servidores (WFE) front-end web e a conta de utilizador que está configurada para o farm de servidores do SharePoint tem de ter acesso à partilha.
      
      ![Ativar o provedor de RBS](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Quando ativar ou desativar RBS, também verá a seguinte mensagem.
      
      ![Configurar o StorSimple adaptador Ativar desativar](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Clique nas **atualização** botão para aplicar a configuração. Quando clica no **atualização** botão, o estado de configuração de RBS será atualizado em todos os servidores WFE, e todo o farm irá ser habilitados para RBS. É apresentada a seguinte mensagem.
      
      ![Mensagem de configuração do adaptador](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Se estiver a configurar RBS para um farm do SharePoint com um grande número de bases de dados (mais de 200), a página da web de Administração Central do SharePoint pode tempo limite. Se isso ocorrer, atualize a página. Isto não afeta o processo de configuração.

4. Verificar a configuração:
   
   1. Inicie sessão no site de Administração Central do SharePoint e navegue para o **configurar o adaptador do StorSimple** página.
   2. Verifique os detalhes de configuração para se certificar de que eles correspondem às definições que introduziu. 
5. Certifique-se de que o RBS funciona corretamente:
   
   1. Carregar um documento no SharePoint. 
   2. Navegue para o caminho UNC que configurou. Certifique-se de que a estrutura de diretórios RBS foi criada e que contém o objeto carregado.
6. (Opcional) Pode utilizar o Microsoft RBS `Migrate()` cmdlet do PowerShell incluído com o SharePoint para migrar o conteúdo BLOB existente para o dispositivo StorSimple. Para obter mais informações, consulte [migrar o conteúdo dentro ou fora do RBS no SharePoint 2013] [ 6] ou [migrar o conteúdo dentro ou fora do RBS (SharePoint Foundation 2010)] [7].
7. (Opcional) Em instalações de teste, pode verificar que os BLOBs foram movidos da base de dados de conteúdo, da seguinte forma: 
   
   1. Inicie o SQL Management Studio.
   2. Execute a consulta ListBlobsInDB_2010.sql ou ListBlobsInDB_2013.sql, da seguinte forma.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Se RBS foi configurado corretamente, deverá aparecer um valor NULL na coluna SizeOfContentInDB para qualquer objeto que foi carregado e externalized com êxito com o RBS.
8. (Opcional) Depois de configurar RBS e mover todo o conteúdo BLOB para o dispositivo StorSimple, pode mover a base de dados de conteúdo para o dispositivo. Se optar por mover a base de dados de conteúdo, recomendamos que configure o armazenamento de base de dados de conteúdo no dispositivo como um volume primário. Em seguida, use estabelecidas práticas recomendadas do SQL Server para migrar a base de dados de conteúdo para o dispositivo StorSimple. 
   
   > [!NOTE]
   > Mover a base de dados de conteúdo para o dispositivo só é suportado para a série StorSimple 8000 (não é suportada para a série de 5000 ou 7000).
   
   Se armazenar BLOBs e a base de dados de conteúdo em volumes separados no dispositivo StorSimple, recomendamos que configure-os no mesmo contentor de volume. Isto garante que eles serão efetuados em conjunto.
   
   > [!WARNING]
   > Se não tiver ativado o RBS, não recomendamos a mover a base de dados de conteúdo para o dispositivo StorSimple. Esta é uma configuração não testada.
   
9. Vá para o passo seguinte: [Configurar a recolha de lixo](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
