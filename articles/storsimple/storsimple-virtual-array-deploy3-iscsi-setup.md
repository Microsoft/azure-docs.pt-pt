---
title: Microsoft Azure StorSimple a instalação do servidor iSCSI da matriz virtual | Microsoft Docs
description: Descreve como executar a configuração inicial, registrar o servidor iSCSI do StorSimple e concluir a instalação do dispositivo.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 4560ca2b07826e2a071f515f147dfab8cbec3624
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516804"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Implantar o StorSimple virtual array – configurado como um servidor iSCSI por meio de portal do Azure

![fluxo do processo de instalação do iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial de implantação se aplica à matriz virtual Microsoft Azure StorSimple. Este tutorial descreve como executar a configuração inicial, registrar o servidor iSCSI do StorSimple, concluir a configuração do dispositivo e, em seguida, criar, montar, inicializar e formatar volumes em sua matriz virtual StorSimple configurada como um servidor iSCSI. 

Os procedimentos descritos aqui levam cerca de 30 minutos a 1 hora para serem concluídos. As informações publicadas neste artigo se aplicam somente a matrizes virtuais StorSimple.

## <a name="setup-prerequisites"></a>Pré-requisitos de instalação

Antes de configurar e configurar sua matriz virtual StorSimple, verifique se:

* Você provisionou uma matriz virtual e se conectou a ela, conforme descrito em [implantar o Storsimple virtual array – provisionar uma matriz virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [implantar o storsimple virtual array – provisionar uma matriz virtual no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Você tem a chave de registro do serviço do StorSimple Device Manager que você criou para gerenciar suas matrizes virtuais do StorSimple. Para obter mais informações, **consulte Etapa 2: Obter a chave** de registro do serviço em [implantar StorSimple virtual array-preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Se esta for a segunda matriz virtual ou subsequente que você está registrando com um serviço de Device Manager StorSimple existente, você deverá ter a chave de criptografia de dados de serviço. Essa chave foi gerada quando o primeiro dispositivo foi registrado com êxito com este serviço. Se você perdeu essa chave, consulte **obter a chave de criptografia de dados de serviço** em [usar a interface do usuário da Web para administrar seu StorSimple virtual array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Configuração passo a passo

Use as instruções passo a passo a seguir para configurar e configurar sua matriz virtual StorSimple:

* [Etapa 1: Conclua a configuração da interface do usuário da Web local e Registre seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Passo 2: Concluir a configuração de dispositivo necessária
* [Etapa 3: Adicionar um volume](#step-3-add-a-volume)
* [Etapa 4: Montar, inicializar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Conclua a configuração da interface do usuário da Web local e Registre seu dispositivo

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a instalação e registrar o dispositivo

1. Abra uma janela do browser. Para se conectar ao tipo de interface do usuário da Web:
   
    `https://<ip-address of network interface>`
   
    Use a URL de conexão anotada na etapa anterior. Você verá um erro notificando que há um problema com o certificado de segurança do site. Clique em **continuar nesta página da Web**.
   
    ![erro de certificado de segurança](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Entre na interface do usuário da Web do seu dispositivo virtual como **StorSimpleAdmin**. Insira a senha de administrador do dispositivo que você alterou na etapa 3: Iniciar o dispositivo virtual em [implantar o Storsimple virtual array-provisionar um dispositivo virtual no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [implantar o storsimple virtual array – provisionar um dispositivo virtual no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Página de entrada](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Você será levado para a **Home** Page. Esta página descreve as várias configurações necessárias para configurar e registrar o dispositivo virtual com o serviço StorSimple Device Manager. Observe que as configurações de **rede**, **as configurações de proxy da Web**e **as configurações de hora** são opcionais. As únicas configurações necessárias são **configurações de dispositivo** e **configurações de nuvem**.
   
    ![Home page](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na página **configurações de rede** , em adaptadores de **rede**, dados 0 serão automaticamente configurados para você. Cada interface de rede é definida por padrão para obter um endereço IP automaticamente (DHCP). Portanto, um endereço IP, uma sub-rede e um gateway serão atribuídos automaticamente (para IPv4 e IPv6).
   
    Ao planejar a implantação do dispositivo como um servidor iSCSI (para provisionar o armazenamento em bloco), recomendamos que você desabilite a opção **obter endereço IP automaticamente** e configure endereços IP estáticos.
   
    ![Página Configurações de rede](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Se você adicionou mais de uma interface de rede durante o provisionamento do dispositivo, você pode configurá-las aqui. Observação Você pode configurar sua interface de rede somente como IPv4 ou como IPv4 e IPv6. Não há suporte para configurações somente IPv6.
5. Os servidores DNS são necessários porque são usados quando o dispositivo tenta se comunicar com seus provedores de serviço de armazenamento em nuvem ou para resolver seu dispositivo por nome se ele estiver configurado como um servidor de arquivos. Na página **configurações de rede** , em **servidores DNS**:
   
   1. Um servidor DNS primário e secundário será configurado automaticamente. Se você optar por configurar endereços IP estáticos, poderá especificar servidores DNS. Para alta disponibilidade, recomendamos que você configure um servidor DNS primário e um secundário.
   2. Clique em **Aplicar**. Isso irá aplicar e validar as configurações de rede.
6. Na página **configurações do dispositivo** :
   
   1. Atribua um **nome** exclusivo ao seu dispositivo. Esse nome pode ter 1-15 caracteres e pode conter letras, números e hifens.
   2. Clique no ícone do ![ **servidor iSCSI** ícone](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) do servidor iSCSI para o **tipo** de dispositivo que você está criando. Um servidor iSCSI permitirá que você provisione o armazenamento em bloco.
   3. Especifique se deseja que este dispositivo seja ingressado no domínio. Se o dispositivo for um servidor iSCSI, ingressar no domínio será opcional. Se você decidir não ingressar o servidor iSCSI em um domínio, clique em **aplicar**, aguarde até que as configurações sejam aplicadas e vá para a próxima etapa.
      
       Se você quiser ingressar o dispositivo em um domínio. Insira um **nome de domínio**e clique em **aplicar**.
      
      > [!NOTE]
      > Se estiver unindo seu servidor iSCSI a um domínio, verifique se sua matriz virtual está em sua própria unidade organizacional (UO) para Microsoft Azure Active Directory e se nenhum GPO (objeto de diretiva de grupo) está aplicado a ele.
      > 
      > 
   4. Será exibida uma caixa de diálogo. Insira suas credenciais de domínio no formato especificado. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). As credenciais de domínio serão verificadas. Você verá uma mensagem de erro se as credenciais estiverem incorretas.
      
       ![credenciais](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Clique em **Aplicar**. Isso irá aplicar e validar as configurações do dispositivo.
7. (Opcionalmente) configure seu servidor proxy da Web. Embora a configuração de proxy da Web seja opcional, lembre-se de que, se você usar um proxy Web, só poderá configurá-lo aqui.
   
    ![configurar proxy Web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na página **proxy Web** :
   
   1. Forneça a **URL do proxy Web** neste formato: *http:\//host-IP address* ou *FQDN: Port Number*. Observe que não há suporte para URLs HTTPS.
   2. Especifique a **autenticação** como **básica** ou **nenhuma**.
   3. Se você estiver usando a autenticação do, também precisará fornecer um **nome de usuário** e uma **senha**.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de proxy da Web definidas.
8. (Opcionalmente) defina as configurações de tempo para seu dispositivo, como fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários porque o dispositivo deve sincronizar o tempo para que ele possa ser autenticado com seus provedores de serviço de nuvem.
   
    ![Definições da hora](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na página **configurações de hora** :
   
   1. Na lista suspensa, selecione o **fuso horário** com base na localização geográfica em que o dispositivo está sendo implantado. O fuso horário padrão para seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
   2. Especifique um **servidor NTP primário** para seu dispositivo ou aceite o valor padrão de time.Windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
   3. Opcionalmente, especifique um **servidor NTP secundário** para seu dispositivo.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de tempo definidas.
9. Defina as configurações de nuvem para seu dispositivo. Nesta etapa, você concluirá a configuração do dispositivo local e registrará o dispositivo no serviço de Device Manager do StorSimple.
   
   1. Insira a **chave de registro do serviço** que você **obteve na etapa 2: Obter a chave** de registro do serviço em [implantar StorSimple virtual array-preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Se esse não for o primeiro dispositivo que você está registrando com esse serviço, você precisará fornecer a **chave de criptografia de dados de serviço**. Essa chave é necessária com a chave de registro do serviço para registrar dispositivos adicionais com o serviço StorSimple Device Manager. Para obter mais informações, consulte [obter a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) em sua interface do usuário da Web local.
   3. Clique em **registrar**. Isso reiniciará o dispositivo. Talvez seja necessário aguardar 2-3 minutos antes que o dispositivo seja registrado com êxito. Depois que o dispositivo for reiniciado, você será levado para a página de entrada.
      
      ![Registar dispositivo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Regresse ao portal do Azure.
11. Navegue até a folha **dispositivos** do seu serviço. Se você tiver muitos recursos, clique em **todos os recursos**, clique no nome do serviço (pesquise por ele, se necessário) e clique em **dispositivos**.
12. Na folha **dispositivos** , verifique se o dispositivo foi conectado com êxito ao serviço pesquisando o status. O estado do dispositivo deve ser **Pronto para configurar**.
    
    ![Registar dispositivo](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Passo 2: Configurar o dispositivo como servidor iSCSI

Execute as etapas a seguir no portal do Azure para concluir a configuração de dispositivo necessária.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Para configurar o dispositivo como servidor iSCSI

1. Vá para o serviço de Device Manager do StorSimple e vá para **gerenciamento > dispositivos**. Na folha **dispositivos** , selecione o dispositivo que você acabou de criar. Este dispositivo aparecerá como **pronto para ser configurado**.
   
    ![Configurar o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Clique no dispositivo e você verá uma mensagem de cabeçalho indicando que o dispositivo está pronto para ser configurado.
   
    ![Configurar o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Clique em **Configurar** na barra de comandos do dispositivo. Isso abre a folha **Configurar** . Na folha **Configurar** , faça o seguinte:
   
   * O nome do servidor iSCSI é populado automaticamente.
   * Verifique se a criptografia de armazenamento em nuvem estádefinida como habilitada. Isso garante que os dados enviados do dispositivo para a nuvem sejam criptografados.
   * Especifique uma chave de criptografia de 32 caracteres e registre-a em um aplicativo de gerenciamento de chaves para referência futura.
   * Selecione uma conta de armazenamento a ser usada com seu dispositivo. Nessa assinatura, você pode selecionar uma conta de armazenamento existente ou pode clicar em **Adicionar** para escolher uma conta de uma assinatura diferente.
     
     ![Configurar o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Clique em **Configurar** para concluir a configuração do servidor iSCSI.
   
    ![Configurar o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Você será notificado de que a criação do servidor iSCSI está em andamento. Depois que o servidor iSCSI for criado com êxito, a folha **dispositivos** será atualizada e o status do dispositivo correspondente será **online**.
   
    ![Configurar o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Passo 3: Adicionar um volume

1. Na folha **dispositivos** , selecione o dispositivo que você acabou de configurar como um servidor iSCSI. Clique em **.** .. (como alternativa, clique com o botão direito do mouse nessa linha) e, no menu de contexto, selecione **Adicionar volume**. Você também pode clicar em **+ Adicionar volume** na barra de comandos. Isso abre a folha **Adicionar volume** .
   
    ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Na folha **Adicionar volume** , faça o seguinte:
   
   * No campo **nome do volume** , insira um nome exclusivo para o volume. O nome deve ser uma cadeia de caracteres que contenha de 3 a 127 caracteres.
   * Na lista suspensa **tipo** , especifique se deseja criar um volume em **camadas** ou **localmente afixado** . Para cargas de trabalho que exigem garantias locais, latências baixas e melhor desempenho, selecione **volume** **fixado localmente** . Para todos os outros dados, selecione **volume**em **camadas** .
   * No campo **capacidade** , especifique o tamanho do volume. Um volume em camadas deve ter entre 500 GB e 5 TB e um volume fixado localmente deve estar entre 50 GB e 500 GB.
     
     Um volume fixado localmente é provisionado de forma densa e garante que os dados primários no volume permaneçam no dispositivo e não sejam despejados para a nuvem.
     
     Um volume em camadas, por outro lado, é provisionado de thin. Quando você cria um volume em camadas, aproximadamente 10% do espaço são provisionados na camada local e 90% do espaço é provisionado na nuvem. Por exemplo, se você provisionou um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seria usado na nuvem quando as camadas de dados. Isso, por sua vez, implica que, se você ficar sem todo o espaço local no dispositivo, não poderá provisionar um compartilhamento em camadas (porque os 10% não estarão disponíveis).
     
     ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Clique em **hosts conectados**, selecione um ACR (registro de controle de acesso) correspondente ao iniciador iSCSI que você deseja conectar a esse volume e, em seguida, clique em **selecionar**. <br><br> 
3. Para adicionar um novo host conectado, clique em **Adicionar novo**, insira um nome para o host e seu nome qualificado iSCSI (iqn) e clique em **Adicionar**. Se você não tiver o IQN, vá para [o apêndice a: Obtenha o IQN de um host](#appendix-a-get-the-iqn-of-a-windows-server-host)do Windows Server.
   
      ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Quando você terminar de configurar o volume, clique em **OK**. Um volume será criado com as configurações especificadas e você verá uma notificação. Por padrão, o monitoramento e o backup serão habilitados para o volume.
   
     ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Para confirmar que o volume foi criado com êxito, vá para a folha **volumes** . Você deve ver o volume listado.
   
   ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Passo 4: Montar, inicializar e formatar um volume

Execute as etapas a seguir para montar, inicializar e formatar os volumes do StorSimple em um host do Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar e formatar um volume

1. Abra o aplicativo **iniciador iSCSI** no servidor apropriado.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Deteção**, clique em **Detetar Portal**.
   
    ![descobrir Portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. Na caixa de diálogo **Detetar Portal de Destino**, forneça o endereço IP da sua interface de rede ativada com permissão para iSCSI e clique em **OK**.
   
    ![Endereço IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Na janela **Propriedades do Iniciador iSCSI**, no separador **Destinos**, localize os **Destinos Detetados**. (Cada volume será um destino descoberto.) O estado do dispositivo deve ser apresentado como **Inativo**.
   
    ![destinos descobertos](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Selecione um dispositivo de destino e clique em **conectar**. Quando o dispositivo estiver ligado, o estado deverá mudar para **Ligado**. (Para obter mais informações sobre como usar o iniciador iSCSI da Microsoft, consulte Instalando e Configurando o [iniciador Microsoft iSCSI][1].
   
    ![selecionar dispositivo de destino](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. No anfitrião do Windows, prima a tecla do Logótipo do Windows + X e clique em **Executar**.
7. Na caixa de diálogo **Executar**, escreva **Diskmgmt.msc**. Clique em **OK**. Será apresentada a caixa de diálogo **Gestão de Discos**. O painel da direita apresentará os volumes no anfitrião.
8. Na janela **Gestão de Discos**, os volumes montados serão apresentados como mostrado na ilustração seguinte. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.
   
    ![gerenciamento de disco](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Clique com o botão direito do mouse e selecione **inicializar disco**.
   
    ![inicializar disco 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Na caixa de diálogo, selecione os discos a serem inicializados e clique em **OK**.
    
    ![inicializar disco 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. O assistente de novo volume simples é iniciado. Selecione um tamanho de disco e clique em **Avançar**.
    
    ![Assistente de novo volume 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Atribua uma letra de unidade ao volume e clique em **Avançar**.
    
    ![Assistente de novo volume 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Insira os parâmetros para formatar o volume. **No Windows Server, há suporte apenas para NTFS.** Defina o tamanho da unidade de alocação para 64K. Forneça um rótulo para seu volume. É uma prática recomendada para esse nome ser idêntica ao nome do volume fornecido em sua matriz virtual StorSimple. Clique em **Seguinte**.
    
    ![Assistente de novo volume 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Verifique os valores do volume e clique em **concluir**.
    
    ![Assistente de novo volume 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Os volumes serão exibidos como **online** na página de **Gerenciamento de disco** .
    
    ![volumes online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba como usar a interface do usuário da Web local para [administrar sua matriz virtual StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apêndice A: Obter o IQN de um anfitrião do Windows Server

Execute os passos seguintes para obter o Nome Qualificado do iSCSI (IQN) de um anfitrião do Windows que está a executar o Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obter o IQN de um anfitrião do Windows

1. Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Configuração**, selecione e copie a cadeia do campo **Nome do Iniciador**.
   
    ![Propriedades do iniciador iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Guarde esta cadeia.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



