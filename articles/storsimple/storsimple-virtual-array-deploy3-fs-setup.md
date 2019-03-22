---
title: Configurar o StorSimple Virtual Array como servidor de ficheiros | Documentos da Microsoft
description: Neste terceiro tutorial de implementação do StorSimple Virtual Array dá instruções ao configurar um dispositivo virtual como servidor de ficheiros.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a931b303e40e41bc23e8b586e1d37e600625b1a8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881066"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Implementar a matriz Virtual StorSimple - conjunto de cópia de segurança como servidor de ficheiros através do portal do Azure
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introdução
Este artigo descreve como efetuar a configuração inicial, registar o servidor de ficheiros do StorSimple, concluir a configuração de dispositivo e criar e ligar a partilhas SMB. Este é o último artigo da série de tutoriais de implementação necessários para implementar completamente sua matriz virtual como um servidor de ficheiros ou um servidor de iSCSI.

O processo de instalação e configuração pode demorar cerca de 10 minutos a concluir. As informações neste artigo aplica-se apenas à implementação da matriz Virtual StorSimple. Para a implantação de dispositivos da série StorSimple 8000, aceda a: [Implementar o dispositivo da série StorSimple 8000 com a atualização 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração
Antes de configurar e configurar a sua matriz Virtual StorSimple, certifique-se de que:

* Ter aprovisionado uma matriz virtual e ligada à mesma, conforme detalhado no [aprovisionar uma matriz Virtual do StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [aprovisionar uma matriz Virtual do StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Tem a chave de registo do serviço do serviço StorSimple Device Manager que criou para gerir as matrizes virtuais do StorSimple. Para obter mais informações, consulte [passo 2: Obter a chave de registo do serviço](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) do StorSimple Virtual Array.
* Se esta for a segunda matriz virtual está a registar um serviço StorSimple Device Manager existente, deve ter a chave de encriptação de dados do serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com êxito com este serviço. Se o ter perdido esta chave, veja [obter a chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) para a sua matriz Virtual StorSimple.

## <a name="step-by-step-setup"></a>Programa de configuração passo a passo
Utilize as seguintes instruções passo a passo para preparar e configurar a sua matriz Virtual StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Concluir a configuração de interface do Usuário da local web e registar o seu dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registar o dispositivo
1. Abra uma janela do browser e ligue à IU da web local. Escreva:
   
   `https://<ip-address of network interface>`
   
   Utilize o URL de ligação que anotou no passo anterior. Verá um erro que indica que existe um problema com o certificado de segurança do Web site. Clique em **continuar para esta página da Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Entrar para a web da interface do Usuário da sua matriz virtual como **StorSimpleAdmin**. Introduza a palavra-passe de administrador dispositivo que alterou no passo 3: Inicie a matriz virtual [aprovisionar uma matriz Virtual do StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou no [aprovisionar uma matriz Virtual do StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. É direcionado para o **home page** página. Esta página descreve as várias configurações necessárias para configurar e registe a matriz virtual com o serviço StorSimple Device Manager. O **as definições de rede**, **definições de proxy Web**, e **definições da hora** são opcionais. As definições necessárias apenas são **definições do dispositivo** e **definições da Cloud**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na **as definições de rede** página sob **interfaces de rede**, dados 0 serão configurados automaticamente para. Cada interface de rede é definido por padrão para obter o endereço IP automaticamente (DHCP). Por conseguinte, um endereço IP, sub-rede e gateway são automaticamente atribuídos (para IPv4 e IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Se tiver adicionado mais de uma interface de rede durante o aprovisionamento do dispositivo, pode configurá-las aqui. Tenha em atenção de que pode configurar a sua interface de rede como IPv4 apenas ou como IPv4 e IPv6. Configurações de apenas de IPv6 não são suportadas.
5. Servidores DNS são necessários porque eles são usados quando o dispositivo tenta comunicar com os fornecedores de serviços de armazenamento na cloud ou para resolver o seu dispositivo por nome quando configurado como um servidor de ficheiros. Na **as definições de rede** página sob a **servidores DNS**:
   
   1. Um servidor DNS primário e secundário são configurados automaticamente. Se optar por configurar endereços IP estáticos, pode especificar servidores DNS. Para elevada disponibilidade, recomendamos que configure um site primário e um servidor DNS secundário.
   2. Clique em **aplicar** para aplicar e validar as definições de rede.
6. Na **definições do dispositivo** página:
   
   1. Atribuir um exclusivo **nome** para o seu dispositivo. Este nome pode ser de 1 a 15 carateres e pode conter letras, números e hífenes.
   2. Clique nas **servidor de ficheiros** ícone ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) para o **tipo** de dispositivo que está a criar. Um servidor de ficheiros permitirá que crie pastas compartilhadas.
   3. Como o dispositivo é um servidor de ficheiros, terá de associar o dispositivo a um domínio. Introduza um **nome de domínio**.
   4. Clique em **Aplicar**.
7. Será apresentada uma caixa de diálogo. Introduza as credenciais de domínio no formato especificado. Clique no ícone de verificação. As credenciais de domínio são verificadas. Verá uma mensagem de erro se as credenciais estão incorretas.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Clique em **Aplicar**. Isso se aplica e validar as definições do dispositivo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Certifique-se de que a sua matriz virtual está em sua própria unidade organizacional (UO) para o Active Directory e não existem objetos de política de grupo (GPO) são aplicados à mesma ou herdados. Política de grupo pode instalar aplicações, tais como o software antivírus na matriz Virtual StorSimple. Instalar software adicional não é suportada e poderia levar a danos nos dados. 
   > 
   > 
9. (Opcionalmente)-configure o servidor de proxy da web. Apesar de configuração do proxy web é opcional, lembre-se de que se utilizar um proxy da web, pode apenas configurá-lo aqui.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na **Web proxy** página:
   
   1. Forneça o **URL de proxy Web** neste formato: *http://&lt;endereço IP do anfitrião ou FQDN&gt;: número de porta*. Tenha em atenção que não são suportados HTTPS URLs.
   2. Especifique **autenticação** como **básica** ou **None**.
   3. Se utilizar a autenticação, também terá de fornecer um **nome de utilizador** e **palavra-passe**.
   4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de proxy da web configurado.
10. (Opcionalmente) configure as definições de hora para o seu dispositivo, como fuso horário e os servidores NTP primário e secundários. Os servidores NTP são necessários porque o dispositivo tem de sincronizar tempo para que ele pode autenticar com o seu fornecedor de serviços cloud.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na **definições da hora** página:
    
    1. Na lista pendente, selecione o **fuso horário** com base na localização geográfica na qual o dispositivo está a ser implementado. O fuso horário predefinido para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
    2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor predefinido de time.windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
    3. Opcionalmente, especifique um **servidor NTP secundário** para o seu dispositivo.
    4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de tempo configurado.
11. Configure as definições da cloud para o seu dispositivo. Neste passo, irá concluir a configuração do dispositivo local e, em seguida, registar o dispositivo com o seu serviço StorSimple Device Manager.
    
    1. Introduza o **chave de registo do serviço** que obteve [passo 2: Obter a chave de registo do serviço](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) do StorSimple Virtual Array.
    2. Se for este seu dispositivo primeira registar neste serviço, será apresentado com o **chave de encriptação de dados do serviço**. Copie esta chave e guarde-a numa localização segura. Esta chave é necessária com a chave de registo de serviço para registar dispositivos adicionais com o serviço StorSimple Device Manager. 
       
       Se não for o primeiro dispositivo que está a registar este serviço, terá de fornecer a chave de encriptação de dados do serviço. Para obter mais informações, consulte para obter o [chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) da interface do Usuário da web no local.
    3. Clique em **registar**. Esta ação irá reiniciar o dispositivo. Terá de aguardar 2 a 3 minutos antes do dispositivo está registado com êxito. Depois do dispositivo for reiniciado, será direcionado para o página de início de sessão.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Regresse ao portal do Azure. Aceda a **todos os recursos**, procure o seu serviço StorSimple Device Manager.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na lista filtrada, selecione o seu serviço StorSimple Device Manager e, em seguida, navegue para **gestão > dispositivos**. Na **dispositivos** painel, certifique-se de que o dispositivo foi ligado com êxito para o serviço e tem o estado **preparado para configurar a**.
    
    ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Passo 2: Configurar o dispositivo como servidor de ficheiros
Execute os seguintes passos no [portal do Azure](https://portal.azure.com/) para concluir a configuração de dispositivo necessários.

#### <a name="to-configure-the-device-as-file-server"></a>Para configurar o dispositivo como servidor de ficheiros
1. Aceda ao seu serviço StorSimple Device Manager e, em seguida, aceda a **gestão > dispositivos**. Na **dispositivos** painel, selecione o dispositivo que acabou de criar. Este dispositivo fosse exibido como **preparado para configurar a**.
   
   ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Clique no dispositivo e verá uma mensagem de faixa a indicar que o dispositivo está pronto para a configuração.
   
    ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Clique em **configurar** na barra de comandos. Esta ação abre o **configurar** painel. Na **configurar** painel, faça o seguinte:
   
   1. O nome do servidor de ficheiros é preenchido automaticamente.
    
   2. Certifique-se a encriptação de armazenamento na cloud está definida como **ativado**. Isto vai encriptar todos os dados que são enviados para a cloud. 
    
   3. Uma chave AES de 256 bits é utilizada com a chave de acesso definidas pelo utilizador para a encriptação. Especifique uma chave de 32 carateres e, em seguida, reintroduza a chave para confirmar que este. Registe a chave num aplicativo de gerenciamento de chaves para referência futura.
    
   4. Clique em **configurar definições necessárias** para especificar as credenciais da conta de armazenamento a ser utilizado com o seu dispositivo. Clique em **adicionar novo** se existem não existem credenciais de conta de armazenamento configurados. **Certifique-se de que a conta de armazenamento que utiliza suporta blobs de blocos. Blobs de páginas não são suportados.** Obter mais informações sobre [bloqueia a blobs e blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Na **adicionar uma credenciais de conta de armazenamento** painel, faça o seguinte: 

    1. Escolha a subscrição atual se a conta de armazenamento está na mesma subscrição que o serviço. Especifique outro é o armazenamento de conta está fora da subscrição do serviço. 
    
    2. Na lista pendente, escolha uma conta de armazenamento existente. 
    
    3. A localização será automaticamente preenchida com base na conta de armazenamento especificada. 
    
    4. Ative o SSL garantir um canal de comunicação de rede segura entre o dispositivo e a cloud.
    
    5. Clique em **adicionar** para adicionar esta credencial de conta de armazenamento. 
   
        ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Assim que a credencial da conta de armazenamento for criada com êxito, o **configurar** painel será atualizado para apresentar as credenciais da conta de armazenamento especificada. Clique em **Configurar**.
   
   ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Verá um que um ficheiro de servidor está a ser criado. Assim que o servidor de ficheiros é criado com êxito, será notificado.
   
   ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   O estado do dispositivo também será alterado para **Online**.
   
   ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Pode avançar para adicionar uma partilha.

## <a name="step-3-add-a-share"></a>Passo 3: Adicionar uma partilha
Execute os seguintes passos no [portal do Azure](https://portal.azure.com/) para criar uma partilha.

#### <a name="to-create-a-share"></a>Para criar uma partilha
1. Selecione o dispositivo de servidor de ficheiros que configurou no passo anterior e clique em **...**  (ou com o botão direito). No menu de contexto, selecione **Adicionar partilha**. Em alternativa, pode clicar em **+ Adicionar partilha** na barra de comando de dispositivo.
   
   ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Especifique as seguintes definições de partilha:

   1. Um nome exclusivo para a sua partilha. O nome tem de ser uma cadeia que contém 3 e 127 carateres.
    
   2. Opcional **Descrição** para a partilha. A descrição ajudará a identificar os proprietários de partilha.
    
   3. R **tipo** para a partilha. O tipo pode ser **em camadas** ou **localmente afixado**, com camadas que está a ser a predefinição. Para cargas de trabalho que necessitem de garantias locais, latências baixas e um desempenho mais elevado, selecione um **localmente afixado** partilhar. Para todos os outros dados, selecione um **em camadas** partilhar.
      Um compartilhamento de localmente afixado é fortemente aprovisionado e assegura que os dados primários no compartilhamento permanecem locais no dispositivo e não transbordam para a cloud. Uma partilha em camadas por outro lado é fracamente aprovisionada. Quando cria uma partilha em camadas, 10% do espaço é aprovisionado no escalão local e 90% do espaço é aprovisionado na cloud. Por exemplo, se tiver aprovisionado um volume de 1 TB, 100 GB seria residem no espaço de local e 900 GB seria usado na cloud quando as camadas de dados. Isso por sua vez, implica que, se executar fora de todo o espaço local no dispositivo, não é possível aprovisionar uma partilha em camadas.
   
   4. Na **predefinir todas as permissões** campo, atribua as permissões para o utilizador ou grupo que está a aceder a esta partilha. Especifique o nome do utilizador ou o grupo de utilizadores no *john\@contoso.com* formato. Recomendamos que utilize um grupo de utilizadores (em vez de um único utilizador) para permitir que os privilégios de administrador para aceder a esses compartilhamentos. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.
   
   5. Clique em **adicionar** para criar a partilha. 
    
       ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Será notificado de que a criação da partilha está em curso.
   
       ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Depois de criar a partilha com as definições especificadas, o **partilhas** painel será atualizado para refletir a nova partilha. Por predefinição, a monitorização e de cópia de segurança estão ativadas para a partilha.
   
      ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Passo 4: Ligar à partilha
Agora terá de ligar a uma ou mais partilhas que criou no passo anterior. Efetue estes passos no anfitrião do Windows Server ligado à sua matriz Virtual StorSimple.

#### <a name="to-connect-to-the-share"></a>Para ligar à partilha de
1. Prima ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. Na janela executar, especifique a *&#92; &#92; &lt;nome do servidor de ficheiros&gt;* como o caminho, substituindo *nome do servidor de ficheiros* com o nome do dispositivo que atribuiu ao ficheiro. servidor. Clique em **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Isso abre Explorador de ficheiros. Agora deve conseguir ver as partilhas que criou como pastas. Selecione e faça duplo clique numa partilha (pasta) para ver o conteúdo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Agora pode adicionar ficheiros a esses compartilhamentos e faça uma cópia de segurança.

## <a name="next-steps"></a>Passos Seguintes
Saiba como utilizar o local da web da interface do Usuário para [administrar a sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

