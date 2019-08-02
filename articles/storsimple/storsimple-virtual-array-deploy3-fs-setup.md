---
title: Configurar a matriz virtual StorSimple como servidor de arquivos | Microsoft Docs
description: Este terceiro tutorial na implantação do StorSimple virtual array instrui você a configurar um dispositivo virtual como servidor de arquivos.
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
ms.openlocfilehash: c2d93099f0f76f173cc7e77ab7f24f27d1560835
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516785"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Implantar o StorSimple virtual array – configurar como servidor de arquivos via portal do Azure
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introdução

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este artigo descreve como executar a configuração inicial, registrar o servidor de arquivos do StorSimple, concluir a configuração do dispositivo e criar e conectar-se a compartilhamentos SMB. Este é o último artigo da série de tutoriais de implantação necessários para implantar completamente sua matriz virtual como um servidor de arquivos ou um servidor iSCSI.

O processo de instalação e configuração pode levar cerca de 10 minutos para ser concluído. As informações neste artigo aplicam-se somente à implantação da matriz virtual StorSimple. Para a implantação de dispositivos StorSimple da série 8000, vá para: [Implante seu dispositivo StorSimple 8000 Series executando a atualização 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Pré-requisitos de instalação
Antes de configurar e configurar sua matriz virtual StorSimple, verifique se:

* Você provisionou uma matriz virtual e se conectou a ela, conforme detalhado em [provisionar um Storsimple Virtual Array no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou provisionar [um storsimple Virtual Array no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Você tem a chave de registro do serviço do StorSimple Device Manager que você criou para gerenciar as matrizes virtuais do StorSimple. Para obter mais informações, [consulte Etapa 2: Obtenha a chave](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) de registro do serviço para o StorSimple virtual array.
* Se esta for a segunda matriz virtual ou subsequente que você está registrando com um serviço de Device Manager StorSimple existente, você deverá ter a chave de criptografia de dados de serviço. Essa chave foi gerada quando o primeiro dispositivo foi registrado com êxito com este serviço. Se você perdeu essa chave, consulte [obter a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) para sua matriz virtual StorSimple.

## <a name="step-by-step-setup"></a>Configuração passo a passo
Use as instruções passo a passo a seguir para configurar e configurar sua matriz virtual StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Conclua a configuração da interface do usuário da Web local e Registre seu dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a instalação e registrar o dispositivo
1. Abra uma janela do navegador e conecte-se à interface do usuário da Web local. Escreva:
   
   `https://<ip-address of network interface>`
   
   Use a URL de conexão anotada na etapa anterior. Você verá um erro indicando que há um problema com o certificado de segurança do site. Clique em **continuar nesta página da Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Entre na interface do usuário da Web da sua matriz virtual como **StorSimpleAdmin**. Insira a senha de administrador do dispositivo que você alterou na etapa 3: Inicie a matriz virtual em [provisionar um Storsimple Virtual Array no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou em provisionar [um storsimple Virtual Array no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Você é levado para a **Home** Page. Esta página descreve as várias configurações necessárias para configurar e registrar a matriz virtual com o serviço StorSimple Device Manager. As configurações de **rede**, **as configurações de proxy da Web**e **as configurações de hora** são opcionais. As únicas configurações necessárias são **configurações de dispositivo** e **configurações de nuvem**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na página **configurações de rede** , em adaptadores de **rede**, dados 0 serão automaticamente configurados para você. Cada interface de rede é definida por padrão para obter o endereço IP automaticamente (DHCP). Portanto, um endereço IP, uma sub-rede e um gateway são atribuídos automaticamente (tanto para IPv4 quanto para IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Se você adicionou mais de uma interface de rede durante o provisionamento do dispositivo, você pode configurá-las aqui. Observação Você pode configurar sua interface de rede somente como IPv4 ou como IPv4 e IPv6. Não há suporte para configurações somente IPv6.
5. Os servidores DNS são necessários porque são usados quando o dispositivo tenta se comunicar com seus provedores de serviço de armazenamento de nuvem ou para resolver seu dispositivo por nome quando configurado como um servidor de arquivos. Na página **configurações de rede** , em **servidores DNS**:
   
   1. Um servidor DNS primário e secundário são configurados automaticamente. Se você optar por configurar endereços IP estáticos, poderá especificar servidores DNS. Para alta disponibilidade, recomendamos que você configure um servidor DNS primário e um secundário.
   2. Clique em **aplicar** para aplicar e validar as configurações de rede.
6. Na página **configurações do dispositivo** :
   
   1. Atribua um **nome** exclusivo ao seu dispositivo. Esse nome pode ter 1-15 caracteres e pode conter letras, números e hifens.
   2. Clique no ícone ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) do servidor de arquivos para o **tipo** de dispositivo que você está criando. Um servidor de arquivos permitirá que você crie pastas compartilhadas.
   3. Como seu dispositivo é um servidor de arquivos, você precisará ingressar o dispositivo em um domínio. Insira um **nome de domínio**.
   4. Clique em **Aplicar**.
7. Será exibida uma caixa de diálogo. Insira suas credenciais de domínio no formato especificado. Clique no ícone de verificação. As credenciais de domínio são verificadas. Você verá uma mensagem de erro se as credenciais estiverem incorretas.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Clique em **Aplicar**. Isso irá aplicar e validar as configurações do dispositivo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Verifique se sua matriz virtual está em sua própria UO (unidade organizacional) para Active Directory e se nenhum GPO (objeto de diretiva de grupo) é aplicado a ela ou herdada. A diretiva de grupo pode instalar aplicativos como software antivírus na matriz virtual StorSimple. Não há suporte para a instalação de software adicional e pode levar à corrupção de dados. 
   > 
   > 
9. (Opcionalmente) configure seu servidor proxy da Web. Embora a configuração de proxy da Web seja opcional, lembre-se de que, se você usar um proxy Web, só poderá configurá-lo aqui.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na página **proxy da Web** :
   
   1. Forneça a **URL do proxy Web** neste formato: *http://&lt;host-endereço IP ou FQDN&gt;:P número classificar*. Observe que não há suporte para URLs HTTPS.
   2. Especifique a **autenticação** como **básica** ou **nenhuma**.
   3. Se estiver usando a autenticação do, você também precisará fornecer um **nome de usuário** e uma **senha**.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de proxy da Web definidas.
10. (Opcionalmente) defina as configurações de tempo para seu dispositivo, como fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários porque o dispositivo deve sincronizar o tempo para que ele possa ser autenticado com seus provedores de serviço de nuvem.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na página **configurações de hora** :
    
    1. Na lista suspensa, selecione o **fuso horário** com base na localização geográfica em que o dispositivo está sendo implantado. O fuso horário padrão para seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
    2. Especifique um **servidor NTP primário** para seu dispositivo ou aceite o valor padrão de time.Windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
    3. Opcionalmente, especifique um **servidor NTP secundário** para seu dispositivo.
    4. Clique em **Aplicar**. Isso validará e aplicará as configurações de tempo definidas.
11. Defina as configurações de nuvem para seu dispositivo. Nesta etapa, você concluirá a configuração do dispositivo local e registrará o dispositivo no serviço de Device Manager do StorSimple.
    
    1. Insira a **chave de registro do serviço** que você [obteve na etapa 2: Obtenha a chave](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) de registro do serviço para o StorSimple virtual array.
    2. Se este for seu primeiro dispositivo registrando-se com esse serviço, você verá a **chave de criptografia de dados de serviço**. Copie esta chave e guarde-a numa localização segura. Essa chave é necessária com a chave de registro do serviço para registrar dispositivos adicionais com o serviço StorSimple Device Manager. 
       
       Se esse não for o primeiro dispositivo que você está registrando com esse serviço, você precisará fornecer a chave de criptografia de dados de serviço. Para obter mais informações, consulte obter a [chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) em sua interface do usuário da Web local.
    3. Clique em **registrar**. Isso reiniciará o dispositivo. Talvez seja necessário aguardar 2-3 minutos antes que o dispositivo seja registrado com êxito. Depois que o dispositivo for reiniciado, você será levado para a página de entrada.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Regresse ao portal do Azure. Vá para **todos os recursos**, pesquise pelo serviço StorSimple Device Manager.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na lista filtrada, selecione o serviço StorSimple Device Manager e, em seguida, navegue até **gerenciamento > dispositivos**. Na folha **dispositivos** , verifique se o dispositivo foi conectado com êxito ao serviço e se o status **está pronto para a configuração**.
    
    ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Passo 2: Configurar o dispositivo como servidor de arquivos
Execute as etapas a seguir no [portal do Azure](https://portal.azure.com/) para concluir a configuração de dispositivo necessária.

#### <a name="to-configure-the-device-as-file-server"></a>Para configurar o dispositivo como servidor de arquivos
1. Vá para o serviço de Device Manager do StorSimple e vá para **gerenciamento > dispositivos**. Na folha **dispositivos** , selecione o dispositivo que você acabou de criar. Este dispositivo aparecerá como **pronto para ser configurado**.
   
   ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Clique no dispositivo e você verá uma mensagem de cabeçalho indicando que o dispositivo está pronto para ser configurado.
   
    ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Clique em **Configurar** na barra de comandos. Isso abre a folha **Configurar** . Na folha **Configurar** , faça o seguinte:
   
   1. O nome do servidor de arquivos é populado automaticamente.
    
   2. Verifique se a criptografia de armazenamento em nuvem estádefinida como habilitada. Isso criptografará todos os dados que são enviados para a nuvem. 
    
   3. Uma chave AES de 256 bits é usada com a chave definida pelo usuário para criptografia. Especifique uma chave de caractere de 32 e insira novamente a chave para confirmá-la. Registre a chave em um aplicativo de gerenciamento de chaves para referência futura.
    
   4. Clique em **definir configurações necessárias** para especificar as credenciais da conta de armazenamento a serem usadas com seu dispositivo. Clique em **Adicionar novo** se não houver nenhuma credencial de conta de armazenamento configurada. **Verifique se a conta de armazenamento que você usa dá suporte a blobs de blocos. Não há suporte para BLOBs de páginas.** Mais informações sobre [blobs de blocos e blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Na folha **Adicionar credenciais da conta de armazenamento** , faça o seguinte: 

    1. Escolha assinatura atual se a conta de armazenamento estiver na mesma assinatura que o serviço. Especificar outro é que a conta de armazenamento está fora da assinatura do serviço. 
    
    2. Na lista suspensa, escolha uma conta de armazenamento existente. 
    
    3. O local será preenchido automaticamente com base na conta de armazenamento especificada. 
    
    4. Habilite o SSL para garantir um canal de comunicação de rede seguro entre o dispositivo e a nuvem.
    
    5. Clique em **Adicionar** para adicionar essa credencial de conta de armazenamento. 
   
        ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Depois que a credencial da conta de armazenamento for criada com êxito, a folha **Configurar** será atualizada para exibir as credenciais da conta de armazenamento especificada. Clique em **Configurar**.
   
   ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Você verá um servidor de arquivos que está sendo criado. Depois que o servidor de arquivos for criado com êxito, você será notificado.
   
   ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   O status do dispositivo também será alterado para **online**.
   
   ![Configurar um servidor de arquivos](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Você pode continuar a adicionar um compartilhamento.

## <a name="step-3-add-a-share"></a>Passo 3: Adicionar uma partilha
Execute os seguintes passos no [portal do Azure](https://portal.azure.com/) para criar uma partilha.

#### <a name="to-create-a-share"></a>Para criar um compartilhamento
1. Selecione o dispositivo de servidor de arquivos configurado na etapa anterior e clique em **.** .. (ou clique com o botão direito do mouse). No menu de contexto, selecione **Adicionar compartilhamento**. Como alternativa, você pode clicar em **+ Adicionar compartilhamento** na barra de comandos do dispositivo.
   
   ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Especifique as seguintes configurações de compartilhamento:

   1. Um nome exclusivo para seu compartilhamento. O nome deve ser uma cadeia de caracteres que contenha de 3 a 127 caracteres.
    
   2. Uma **Descrição** opcional para o compartilhamento. A descrição ajudará a identificar os proprietários do compartilhamento.
    
   3. Um **tipo** para o compartilhamento. O tipo pode ser em **camadas** ou **fixado localmente**, com camadas sendo o padrão. Para cargas de trabalho que exigem garantias locais, latências baixas e melhor desempenho, selecione um compartilhamento **fixado localmente** . Para todos os outros dados, selecione um compartilhamento em **camadas** .
      Um compartilhamento fixado localmente é provisionado de forma espessa e garante que os dados primários no compartilhamento permaneçam locais com o dispositivo e não sejam despejados para a nuvem. Um compartilhamento em camadas, por outro lado, é provisionado de thin. Quando você cria um compartilhamento em camadas, 10% do espaço são provisionados na camada local e 90% do espaço é provisionado na nuvem. Por exemplo, se você provisionou um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seria usado na nuvem quando as camadas de dados. Isso, por sua vez, implica que, se você ficar sem todo o espaço local no dispositivo, não poderá provisionar um compartilhamento em camadas.
   
   4. No campo **definir permissões totais padrão para** , atribua as permissões para o usuário ou o grupo que está acessando esse compartilhamento. Especifique o nome do usuário ou o grupo de usuários no *formato\@John contoso.com* . Recomendamos que você use um grupo de usuários (em vez de um único usuário) para permitir que privilégios de administrador acessem esses compartilhamentos. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.
   
   5. Clique em **Adicionar** para criar o compartilhamento. 
    
       ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Será notificado de que a criação da partilha está em curso.
   
       ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Depois que o compartilhamento for criado com as configurações especificadas, a folha compartilhamentos será atualizada para refletir o novo compartilhamento. Por padrão, o monitoramento e o backup estão habilitados para o compartilhamento.
   
      ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Passo 4: Ligar à partilha
Agora, você precisará se conectar a um ou mais compartilhamentos que você criou na etapa anterior. Execute estas etapas no host do Windows Server conectado à sua matriz virtual StorSimple.

#### <a name="to-connect-to-the-share"></a>Para se conectar ao compartilhamento
1. Pressione ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. Na janela Executar, especifique o  *&#92; &#92; &lt;nome&gt; do servidor de arquivos* como o caminho, substituindo o nome do servidor de *arquivos* pelo nome do dispositivo que você atribuiu ao servidor de arquivos. Clique em **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Isso abre o explorador de arquivos. Agora deve conseguir ver as partilhas que criou como pastas. Selecione e faça duplo clique numa partilha (pasta) para ver o conteúdo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Agora você pode adicionar arquivos a esses compartilhamentos e fazer um backup.

## <a name="next-steps"></a>Passos Seguintes
Saiba como usar a interface do usuário da Web local para [administrar sua matriz virtual StorSimple](storsimple-ova-web-ui-admin.md).

