---
title: Configurar o StorSimple Virtual Array como servidor de ficheiros [ Microsoft Docs
description: Este terceiro tutorial na implementação storSimple Virtual Array instrui-o a configurar um dispositivo virtual como servidor de ficheiros.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16a5e0bb3e50e3a90951572e8d2847d379c1b114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297637"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Implementar O Conjunto Virtual StorSimple - Configurar como servidor de ficheiros através do portal Azure
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introdução

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este artigo descreve como executar a configuração inicial, registar o seu servidor de ficheiroS StorSimple, completar a configuração do dispositivo e criar e ligar-se às ações da SMB. Este é o último artigo da série de tutoriais de implementação necessários para implementar completamente a sua matriz virtual como servidor de ficheiros ou servidor iSCSI.

O processo de configuração e configuração pode demorar cerca de 10 minutos a ser concluído. As informações deste artigo aplicam-se apenas à implementação do StorSimple Virtual Array. Para a implementação de dispositivos da série StorSimple 8000, vá para: [Implemente o seu dispositivo da série StorSimple 8000 a executar o Update 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração
Antes de configurar e configurar o seu StorSimple Virtual Array, certifique-se de que:

* Você forprovisionou uma matriz virtual e ligou-a conforme detalhado na [Disposição de um StorSimple Virtual Array em Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [Provision a StorSimple Virtual Array em VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Tem a chave de registo de serviço do serviço StorSimple Device Manager que criou para gerir as Matrizes Virtuais StorSimple. Para mais informações, consulte [passo 2: Obtenha a chave](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) de registo de serviço para StorSimple Virtual Array.
* Se esta for a segunda ou posterior matriz virtual que está a registar com um serviço storSimple Device Manager existente, deverá ter a chave de encriptação de dados de serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com sucesso com este serviço. Se perdeu esta chave, consulte a chave de [encriptação](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) de dados de serviço para o seu StorSimple Virtual Array.

## <a name="step-by-step-setup"></a>Configuração passo a passo
Utilize as seguintes instruções passo a passo para configurar e configurar o seu StorSimple Virtual Array.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Complete a configuração de UI web local e registe o seu dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Para completar a configuração e registar o dispositivo
1. Abra uma janela do navegador e ligue-se à UI web local. Escreva:
   
   `https://<ip-address of network interface>`
   
   Utilize o URL de ligação anotado no passo anterior. Vê um erro que indica que há um problema com o certificado de segurança do site. Clique **Em Continuar nesta página web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Inscreva-se na Web UI da sua matriz virtual como **StorSimpleAdmin**. Introduza a palavra-passe do administrador do dispositivo que alterou no Passo 3: Inicie a matriz virtual na [Provision a StorSimple Virtual Array em Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou na [Provision a StorSimple Virtual Array em VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Foi levado para a página **inicial.** Esta página descreve as várias definições necessárias para configurar e registar a matriz virtual com o serviço StorSimple Device Manager. As definições de **Rede,** **definições**de proxy web e **definições de tempo** são opcionais. As únicas definições necessárias são **as definições do Dispositivo** e as **definições de Cloud**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na página de **definições** da Rede em interfaces de **Rede,** o DATA 0 será automaticamente configurado para si. Cada interface de rede é definida por predefinição para obter o endereço IP automaticamente (DHCP). Assim, um endereço IP, subnet e gateway são automaticamente atribuídos (tanto para iPv4 como IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Se tiver adicionado mais de uma interface de rede durante o fornecimento do dispositivo, pode configurá-los aqui. Note que pode configurar a sua interface de rede apenas como IPv4 ou como IPv4 e IPv6. Apenas as configurações do IPv6 não são suportadas.
5. Os servidores DNS são necessários porque são utilizados quando o seu dispositivo tenta comunicar com os seus fornecedores de serviços de armazenamento em nuvem ou para resolver o seu dispositivo pelo nome quando configurado como servidor de ficheiros. Na página de **definições** da Rede sob os **servidores DNS:**
   
   1. Um servidor DNS primário e secundário é configurado automaticamente. Se optar por configurar endereços IP estáticos, pode especificar servidores DNS. Para uma elevada disponibilidade, recomendamos que configure um servidor DNS primário e secundário.
   2. Clique **Em Aplicar** para aplicar e validar as definições de rede.
6. Na página de definições do **Dispositivo:**
   
   1. Atribuir um **nome** único ao seu dispositivo. Este nome pode ser de 1-15 caracteres e pode conter letras, números e hífenes.
   2. Clique no ícone ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) do **servidor de ficheiros** para o **tipo** de dispositivo que está a criar. Um servidor de ficheiros permitir-lhe-á criar pastas partilhadas.
   3. Como o seu dispositivo é um servidor de ficheiros, terá de se juntar ao dispositivo num domínio. Introduza um **nome de domínio**.
   4. Clique em **Aplicar**.
7. Aparecerá uma caixa de diálogo. Introduza as suas credenciais de domínio no formato especificado. Clique no ícone de verificação. As credenciais de domínio são verificadas. Vê uma mensagem de erro se as credenciais estiverem incorretas.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Clique em **Aplicar**. Isto aplicará e validará as definições do dispositivo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e que não são aplicados ou herdados objetos de política de grupo (GPO). A política do grupo pode instalar aplicações como software antivírus no StorSimple Virtual Array. A instalação de software adicional não é suportada e pode levar à corrupção de dados. 
   > 
   > 
9. (Opcionalmente) configure o seu servidor de procuração web. Apesar de a configuração do proxy Web ser opcional, tenha em atenção que se utilizar um proxy Web, só pode configurá-lo aqui.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na página de **procuração da Web:**
   
   1. Forneça o URL de **procuração web** neste formato: *&lt;http:// endereço ip do anfitrião ou número&gt;fQDN :Port*. Note que os URLs HTTPS não são suportados.
   2. Especificar **autenticação** como **Básico** ou **Nenhum**.
   3. Se utilizar a autenticação, também terá de fornecer um nome de **utilizador** e **palavra-passe**.
   4. Clique em **Aplicar**. Isto validará e aplicará as definições configuradas de procuração web.
10. (Opcionalmente) configure as definições de tempo para o seu dispositivo, como fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na página de definições do **Tempo:**
    
    1. A partir da lista de abandono, selecione o **fuso horário** com base na localização geográfica em que o dispositivo está a ser implantado. O fuso horário predefinido para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
    2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor padrão de time.windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
    3. Especificar opcionalmente um **servidor NTP secundário** para o seu dispositivo.
    4. Clique em **Aplicar**. Isto validará e aplicará as definições de tempo configuradas.
11. Configure as definições da nuvem para o seu dispositivo. Neste passo, irá completar a configuração do dispositivo local e, em seguida, registar o dispositivo com o seu serviço StorSimple Device Manager.
    
    1. Introduza a chave de registo do **Serviço** que obteve no [Passo 2: Obtenha a chave](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) de registo de serviço para StorSimple Virtual Array.
    2. Se este for o seu primeiro dispositivo a registar-se com este serviço, será apresentado com a chave de **encriptação**de dados do Serviço . Copie esta chave e guarde-a numa localização segura. Esta chave é necessária com a chave de registo de serviço para registar dispositivos adicionais com o serviço StorSimple Device Manager. 
       
       Se este não for o primeiro dispositivo que está a registar com este serviço, terá de fornecer a chave de encriptação de dados de serviço. Para mais informações, consulte a chave de [encriptação](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) de dados do serviço na sua UI web local.
    3. Clique no **Registo**. Isto reiniciará o dispositivo. Pode ter de esperar 2-3 minutos antes de o dispositivo ser registado com sucesso. Depois de o dispositivo ter reiniciado, será levado para a placa na página.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Regresse ao portal do Azure. Vá a **Todos os recursos,** procure o seu serviço StorSimple Device Manager.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na lista filtrada, selecione o serviço StorSimple Device Manager e, em seguida, navegue para **Management > Devices**. Na lâmina **do Dispositivo,** verifique se o dispositivo ligou com sucesso ao serviço e tem o estado **pronto a configurar**.
    
    ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Passo 2: Configure o dispositivo como servidor de ficheiros
Execute os seguintes passos no [portal Azure](https://portal.azure.com/) para completar a configuração do dispositivo necessário.

#### <a name="to-configure-the-device-as-file-server"></a>Para configurar o dispositivo como servidor de ficheiros
1. Vá ao seu serviço StorSimple Device Manager e depois vá para **a Management > Devices**. Na lâmina **dispositivos,** selecione o dispositivo que acabou de criar. Este dispositivo apareceria como **Pronto para configurar**.
   
   ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Clique no dispositivo e verá uma mensagem de banner indicando que o dispositivo está pronto para configurar.
   
    ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Clique em **Configurar** na barra de comando. Isto abre a lâmina **Configure.** Na lâmina **Configure,** faça o seguinte:
   
   1. O nome do servidor de ficheiros é automaticamente povoado.
    
   2. Certifique-se de que a encriptação de armazenamento em nuvem está definida para **ativar**. Isto irá encriptar todos os dados enviados para a nuvem. 
    
   3. Uma tecla AES de 256 bits é usada com a chave definida pelo utilizador para encriptação. Especifique uma tecla de caracteres de 32 caracteres e, em seguida, reintroduza a chave para confirmá-la. Grave a chave numa aplicação de gestão chave para referência futura.
    
   4. Clique em **Configurar as definições necessárias** para especificar as credenciais de conta de armazenamento a utilizar com o seu dispositivo. Clique **Em adicionar novo** se não houver credenciais de conta de armazenamento configuradas. **Certifique-se de que a conta de armazenamento que utiliza suporta bolhas de blocos. As bolhas de página não são suportadas.** Mais informações sobre [blocos de bolhas e bolhas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)de página.
   
      ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Na lâmina **de credenciais de conta de armazenamento Adicionar,** faça o seguinte: 

    1. Escolha a subscrição atual se a conta de armazenamento estiver na mesma subscrição que o serviço. Especificar outra é que a conta de armazenamento está fora da subscrição do serviço. 
    
    2. Na lista de abandono, escolha uma conta de armazenamento existente. 
    
    3. A localização será automaticamente povoada com base na conta de armazenamento especificada. 
    
    4. Ative o TLS para garantir um canal de comunicação de rede seguro entre o dispositivo e a nuvem.
    
    5. Clique em **Adicionar** esta credencial de conta de armazenamento. 
   
        ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Uma vez criada a credencial da conta de armazenamento com sucesso, a lâmina **Configure** será atualizada para exibir as credenciais de conta de armazenamento especificadas. Clique em **Configurar**.
   
   ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Verá que está a ser criado um servidor de ficheiros. Assim que o servidor de ficheiros for criado com sucesso, será notificado.
   
   ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   O estado do dispositivo também mudará para **Online**.
   
   ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Pode continuar a adicionar uma parte.

## <a name="step-3-add-a-share"></a>Passo 3: Adicionar uma parte
Execute os seguintes passos no [portal do Azure](https://portal.azure.com/) para criar uma partilha.

#### <a name="to-create-a-share"></a>Para criar uma parte
1. Selecione o dispositivo de servidor de ficheiros configurado no passo anterior e clique **em ...** (ou clique na direita). No menu de contexto, selecione **Adicionar partilha**. Em alternativa, pode clicar **em + Adicionar Partilhar** na barra de comando do dispositivo.
   
   ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Especifique as seguintes definições de partilha:

   1. Um nome único para a sua parte. O nome deve ser uma corda que contenha 3 a 127 caracteres.
    
   2. Uma **descrição** opcional para a parte. A descrição ajudará a identificar os proprietários de ações.
    
   3. Um **tipo** para a parte. O tipo pode ser **hierárquico** ou **fixado localmente,** sendo o nível do padrão. Para cargas de trabalho que requerem garantias locais, baixas lestabeleceções e maior desempenho, selecione uma parte **fixa local.** Para todos os outros dados, selecione uma parte **Tiered.**
      Uma parte fixada localmente é densamente aprovisionada e garante que os dados primários sobre a partilha permanecem locais no dispositivo e não derramam para a nuvem. Uma parte hierárquica, por outro lado, é escassamente provisionada. Quando se cria uma quota hierárquica, 10% do espaço é aprovisionado no nível local e 90% do espaço está aprovisionado na nuvem. Por exemplo, se você provisionasse um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seria usado na nuvem quando os níveis de dados. Isto, por sua vez, implica que se ficar sem todo o espaço local do dispositivo, não pode fornecer uma parte hierárquica.
   
   4. No **Conjunto predefinido permissões completas para** o campo, atribua as permissões ao utilizador ou ao grupo que está a aceder a esta partilha. Especifique o nome do utilizador ou do grupo utilizador no formato *\@John contoso.com.* Recomendamos que utilize um grupo de utilizadores (em vez de um único utilizador) para permitir que os privilégios administrativos acedam a estas ações. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.
   
   5. Clique em **Adicionar** para criar a parte. 
    
       ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Será notificado de que a criação da partilha está em curso.
   
       ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Após a criação da parte com as definições especificadas, a lâmina **De partilha** sairá atualizada para refletir a nova ação. Por predefinição, a monitorização e a cópia de segurança estão ativadas para a partilha.
   
      ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Passo 4: Ligar à parte
Terá agora de se ligar a uma ou mais ações que criou no passo anterior. Execute estes passos no seu anfitrião do Windows Server ligado ao seu StorSimple Virtual Array.

#### <a name="to-connect-to-the-share"></a>Para ligar à parte
1. Prima ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. Na janela Executar, especifique o * &lt;&gt; nome do servidor de ficheiros&#92;&#92;* como o caminho, substituindo o nome do servidor de *ficheiros* pelo nome do dispositivo que atribuiu ao seu servidor de ficheiros. Clique em **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Isto abre o File Explorer. Agora deve conseguir ver as partilhas que criou como pastas. Selecione e faça duplo clique numa partilha (pasta) para ver o conteúdo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Agora pode adicionar ficheiros a estas ações e obter uma cópia de segurança.

## <a name="next-steps"></a>Passos seguintes
Aprenda a usar a UI web local para [administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

