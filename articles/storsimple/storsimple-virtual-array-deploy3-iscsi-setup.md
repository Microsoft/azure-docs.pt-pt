---
title: Microsoft Azure StorSimple Virtual Array iSCSI configuração do servidor [ Microsoft Azure StorSimple Virtual Array iSCSI] Microsoft Docs
description: Descreve como realizar a configuração inicial, registar o seu servidor StorSimple iSCSI e configurar o dispositivo completo.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254498"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Implemente o StorSimple Virtual Array – Configurado como servidor iSCSI via portal Azure

![fluxo de processo de configuração iscsi](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial de implementação aplica-se ao Microsoft Azure StorSimple Virtual Array. Este tutorial descreve como executar a configuração inicial, registar o seu servidor StorSimple iSCSI, completar a configuração do dispositivo e, em seguida, criar, montar, inicializar e formato volumes no seu StorSimple Virtual Array configurado como um servidor iSCSI. 

Os procedimentos aqui descritos demoram aproximadamente 30 minutos a 1 hora para ser concluídos. A informação publicada neste artigo aplica-se apenas ao StorSimple Virtual Arrays.

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração

Antes de configurar e configurar o seu StorSimple Virtual Array, certifique-se de que:

* Você forprovisionou uma matriz virtual e ligou-a como descrito no [Deploy StorSimple Virtual Array - Fornecer uma matriz virtual em Hiper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [implementar Matriz Virtual StorSimple - Fornecer uma matriz virtual em VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Tem a chave de registo de serviço do serviço StorSimple Device Manager que criou para gerir as suas Matrizes Virtuais StorSimple. Para mais informações, consulte **passo 2: Obtenha a chave** de registo de serviço [storSimple Virtual Array - Prepare o portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Se esta for a segunda ou posterior matriz virtual que está a registar com um serviço storSimple Device Manager existente, deverá ter a chave de encriptação de dados de serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com sucesso com este serviço. Se perdeu esta chave, consulte A chave de **encriptação** de dados de serviço em [Use o Web UI para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Configuração passo a passo

Utilize as seguintes instruções passo a passo para configurar e configurar o seu StorSimple Virtual Array:

* [Passo 1: Complete a configuração de UI web local e registe o seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Passo 2: Complete a configuração do dispositivo necessário
* [Passo 3: Adicionar um volume](#step-3-add-a-volume)
* [Passo 4: Montar, inicializar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Complete a configuração de UI web local e registe o seu dispositivo

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para completar a configuração e registar o dispositivo

1. Abra uma janela do browser. Para ligar ao tipo ui web:
   
    `https://<ip-address of network interface>`
   
    Utilize o URL de ligação anotado no passo anterior. Verá um erro notificando-o de que há um problema com o certificado de segurança do site. Clique **Em Continuar nesta página web**.
   
    ![erro de certificado de segurança](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Inscreva-se na Web UI do seu dispositivo virtual como **StorSimpleAdmin**. Introduza a palavra-passe do administrador do dispositivo que alterou no Passo 3: Inicie o dispositivo virtual no [Desdobramento StorSimple Virtual Array - Fornecer um dispositivo virtual em Hiper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [implementar StorSimple Virtual Array - Fornecer um dispositivo virtual em VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Página de sessão](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Será levado para a página **inicial.** Esta página descreve as várias definições necessárias para configurar e registar o dispositivo virtual com o serviço StorSimple Device Manager. Note que as **definições**de rede, **definições**de proxy web e **definições** de tempo são opcionais. As únicas definições necessárias são **as definições do Dispositivo** e as **definições de Cloud**.
   
    ![Página de boas-vindas](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na página de **definições** da Rede em interfaces de **Rede,** o DATA 0 será automaticamente configurado para si. Cada interface de rede é definida por predefinição para obter um endereço IP automaticamente (DHCP). Portanto, um endereço IP, subnet e gateway serão automaticamente atribuídos (tanto para iPv4 como IPv6).
   
    Como planeia implementar o seu dispositivo como servidor iSCSI (para fornecer armazenamento de blocos), recomendamos que desative automaticamente o **endereço IP** e configure endereços IP estáticos.
   
    ![Página de definições de rede](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Se tiver adicionado mais de uma interface de rede durante o fornecimento do dispositivo, pode configurá-los aqui. Note que pode configurar a sua interface de rede apenas como IPv4 ou como IPv4 e IPv6. Apenas as configurações do IPv6 não são suportadas.
5. Os servidores DNS são necessários porque são utilizados quando o seu dispositivo tenta comunicar com os seus fornecedores de serviços de armazenamento em nuvem ou para resolver o seu dispositivo pelo nome se estiver configurado como um servidor de ficheiros. Na página de **definições** da Rede sob os **servidores DNS:**
   
   1. Um servidor DNS primário e secundário será configurado automaticamente. Se optar por configurar endereços IP estáticos, pode especificar servidores DNS. Para uma elevada disponibilidade, recomendamos que configure um servidor DNS primário e secundário.
   2. Clique em **Aplicar**. Isto aplicará e validará as definições da rede.
6. Na página de definições do **Dispositivo:**
   
   1. Atribuir um **nome** único ao seu dispositivo. Este nome pode ser de 1-15 caracteres e pode conter letras, números e hífenes.
   2. Clique](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) no ícone do servidor ![ **iSCSI** iSCSI para o **tipo** de dispositivo que está a criar. Um servidor iSCSI permitir-lhe-á fornecer armazenamento de blocos.
   3. Especifique se pretende que este dispositivo seja unido ao domínio. Se o seu dispositivo for um servidor iSCSI, então a adesão ao domínio é opcional. Se decidir não juntar o seu servidor iSCSI a um domínio, clique em **Aplicar,** aguarde que as definições sejam aplicadas e, em seguida, salte para o passo seguinte.
      
       Se quiser juntar o dispositivo a um domínio. Introduza um **nome de domínio**e, em seguida, clique em **Aplicar**.
      
      > [!NOTE]
      > Se juntar o seu servidor iSCSI a um domínio, certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Microsoft Azure Ative Directory e não são aplicados objetos de política de grupo (GPO).
      > 
      > 
   4. Aparecerá uma caixa de diálogo. Introduza as suas credenciais de domínio no formato especificado. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). As credenciais de domínio serão verificadas. Verá uma mensagem de erro se as credenciais estiverem incorretas.
      
       ![credenciais](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Clique em **Aplicar**. Isto aplicará e validará as definições do dispositivo.
7. (Opcionalmente) configure o seu servidor de procuração web. Apesar de a configuração do proxy Web ser opcional, tenha em atenção que se utilizar um proxy Web, só pode configurá-lo aqui.
   
    ![configurar procuração web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na página de **procuração da Web:**
   
   1. Forneça o URL de **procuração web** neste formato: *http:\//endereço ip de anfitrião* ou *número de porta FQDN:.* Note que os URLs HTTPS não são suportados.
   2. Especificar **autenticação** como **Básico** ou **Nenhum**.
   3. Se estiver a utilizar a autenticação, também terá de fornecer um nome de **utilizador** e **palavra-passe.**
   4. Clique em **Aplicar**. Isto validará e aplicará as definições configuradas de procuração web.
8. (Opcionalmente) configure as definições de tempo para o seu dispositivo, como fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.
   
    ![Definições de tempo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na página de definições do **Tempo:**
   
   1. A partir da lista de lançamentos, selecione o **fuso horário** com base na localização geográfica em que o dispositivo está a ser implantado. O fuso horário predefinido para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
   2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor padrão de time.windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
   3. Especificar opcionalmente um **servidor NTP secundário** para o seu dispositivo.
   4. Clique em **Aplicar**. Isto validará e aplicará as definições de tempo configuradas.
9. Configure as definições da nuvem para o seu dispositivo. Neste passo, irá completar a configuração do dispositivo local e, em seguida, registar o dispositivo com o seu serviço StorSimple Device Manager.
   
   1. Introduza a chave de registo do **Serviço** que obteve no **Passo 2: Obtenha a chave** de registo de serviço no Deploy [StorSimple Virtual Array - Prepare o Portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Se este não for o primeiro dispositivo que está a registar com este serviço, terá de fornecer a chave de **encriptação**de dados do Serviço . Esta chave é necessária com a chave de registo de serviço para registar dispositivos adicionais com o serviço StorSimple Device Manager. Para mais informações, consulte a chave de [encriptação](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) de dados do serviço na sua UI web local.
   3. Clique no **Registo**. Isto reiniciará o dispositivo. Pode ter de esperar 2-3 minutos antes de o dispositivo ser registado com sucesso. Depois de o dispositivo ter reiniciado, será levado para a placa na página.
      
      ![Dispositivo de registo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Regresse ao portal do Azure.
11. Navegue para a lâmina de **dispositivos** do seu serviço. Se tiver muitos recursos, clique em **Todos os recursos,** clique no seu nome de serviço (procure-o se necessário) e, em seguida, clique em **Dispositivos**.
12. Na lâmina **do Dispositivo,** verifique se o dispositivo ligou com sucesso ao serviço, procurando o estado. O estado do dispositivo deve ser **Pronto para configurar**.
    
    ![Dispositivo de registo](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Passo 2: Configure o dispositivo como servidor iSCSI

Execute os seguintes passos no portal Azure para completar a configuração do dispositivo necessário.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Para configurar o dispositivo como servidor iSCSI

1. Vá ao seu serviço StorSimple Device Manager e depois vá para **a Management > Devices**. Na lâmina **dispositivos,** selecione o dispositivo que acabou de criar. Este dispositivo apareceria como **Pronto para configurar**.
   
    ![Configure o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Clique no dispositivo e verá uma mensagem de banner indicando que o dispositivo está pronto para configurar.
   
    ![Configure o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Clique em **Configurar** na barra de comando do dispositivo. Isto abre a lâmina **Configure.** Na lâmina **Configure,** faça o seguinte:
   
   * O nome do servidor iSCSI é automaticamente povoado.
   * Certifique-se de que a encriptação de armazenamento em nuvem está definida para **ativar**. Isto garante que os dados enviados do dispositivo para a nuvem estão encriptados.
   * Especifique uma chave de encriptação de 32 caracteres e grave-a numa aplicação de gestão chave para referência futura.
   * Selecione uma conta de armazenamento a utilizar com o seu dispositivo. Nesta subscrição, pode selecionar uma conta de armazenamento existente, ou pode clicar em **Adicionar** para escolher uma conta a partir de uma subscrição diferente.
     
     ![Configure o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Clique em **Configurar** para completar a configuração do servidor iSCSI.
   
    ![Configure o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Será notificado de que a criação do servidor iSCSI está em andamento. Após a criação do servidor iSCSI com sucesso, a lâmina **do Dispositivo** é atualizada e o estado do dispositivo correspondente é **Online**.
   
    ![Configure o dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Passo 3: Adicionar um volume

1. Na lâmina **dispositivos,** selecione o dispositivo que acabou de configurar como servidor iSCSI. Clique **em ...** (alternativamente clique à direita nesta linha) e a partir do menu de contexto, selecione Adicionar **volume**. Também pode clicar **+ Adicionar volume** a partir da barra de comando. Isto abre a lâmina de **volume Adicionar.**
   
    ![Adicione um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Na lâmina de **volume Adicionar,** faça o seguinte:
   
   * No campo **de nome volume,** introduza um nome único para o seu volume. O nome deve ser uma corda que contenha 3 a 127 caracteres.
   * Na lista de abandono do **Tipo,** especifique se criar á criação de um volume **hierárquico** ou **fixado localmente.** Para cargas de trabalho que requerem garantias locais, baixas llátências e maior desempenho, selecione **volume** **fixado localmente.** Para todos os outros dados, selecione **volume** **tiered** .
   * No campo **capacidade,** especifique o tamanho do volume. Um volume hierárquico deve ser entre 500 GB e 5 TB e um volume fixado localmente deve ser entre 50 GB e 500 GB.
     
     Um volume fixado localmente é densamente provisionado e garante que os dados primários do volume permanecem no dispositivo e não derramam para a nuvem.
     
     Um volume hierárquico, por outro lado, é pouco provisionado. Quando se cria um volume hierárquico, aproximadamente 10% do espaço é aprovisionado no nível local e 90% do espaço está aprovisionado na nuvem. Por exemplo, se for provisionado um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seriam usados na nuvem quando os níveis de dados. Isto, por sua vez, implica que se ficar sem todo o espaço local do dispositivo, não pode fornecer uma parte hierárquica (porque os 10% não estarão disponíveis).
     
     ![Adicione um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Clique em **anfitriões Conectados,** selecione um registo de controlo de acesso (ACR) correspondente ao iniciador iSCSI que pretende ligar a este volume e, em seguida, clique em **Selecionar**. <br><br> 
3. Para adicionar um novo anfitrião conectado, clique **em Adicionar novo,** insira um nome para o anfitrião e o seu nome iSCSI Qualificado (IQN), e, em seguida, clique em **Adicionar**. Se não tiver o IQN, vá ao [Apêndice A: Obtenha o IQN de um anfitrião do Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Adicione um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Quando terminar de configurar o seu volume, clique em **OK**. Será criado um volume com as definições especificadas e verá uma notificação. Por defeito, a monitorização e a cópia de segurança serão ativadas para o volume.
   
     ![Adicione um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Para confirmar que o volume foi criado com sucesso, vá à lâmina **Volumes.** Devia ver o volume listado.
   
   ![Adicione um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Passo 4: Montar, inicializar e formatar um volume

Execute os seguintes passos para montar, inicializar e formatar os volumes StorSimple num anfitrião do Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar e formatar um volume

1. Abra a aplicação **de iniciação iSCSI** no servidor apropriado.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Deteção**, clique em **Detetar Portal**.
   
    ![descobrir portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. Na caixa de diálogo **Detetar Portal de Destino**, forneça o endereço IP da sua interface de rede ativada com permissão para iSCSI e clique em **OK**.
   
    ![Endereço IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Na janela **Propriedades do Iniciador iSCSI**, no separador **Destinos**, localize os **Destinos Detetados**. (Cada volume será um alvo descoberto.) O estado do dispositivo deve aparecer como **Inativo**.
   
    ![alvos descobertos](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Selecione um dispositivo-alvo e, em seguida, clique em **Ligar**. Quando o dispositivo estiver ligado, o estado deverá mudar para **Ligado**. (Para obter mais informações sobre a utilização do iniciador Microsoft iSCSI, consulte [instalação e configuração do iniciador iSCSI][1]da Microsoft .
   
    ![selecionar dispositivo-alvo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. No anfitrião do Windows, prima a tecla do Logótipo do Windows + X e clique em **Executar**.
7. Na caixa de diálogo **Executar**, escreva **Diskmgmt.msc**. Clique em **OK**. Será apresentada a caixa de diálogo **Gestão de Discos**. O painel da direita apresentará os volumes no anfitrião.
8. Na janela **Gestão de Discos**, os volumes montados serão apresentados como mostrado na ilustração seguinte. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.
   
    ![gestão de discos](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Clique à direita e **selecione Inicialize o Disco**.
   
    ![inicializar o disco 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Na caixa de diálogo, selecione o disco(s) para inicializar e, em seguida, clique **OK**.
    
    ![inicializar o disco 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. O novo assistente de volume simples começa. Selecione um tamanho de disco e, em seguida, clique **em Seguinte**.
    
    ![novo assistente de volume 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Atribuir uma carta de unidade ao volume e, em seguida, clicar **em Seguinte**.
    
    ![novo assistente de volume 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Introduza os parâmetros para formatar o volume. **No Windows Server, apenas o NTFS é suportado.** Detete o tamanho da unidade de alocação para 64K. Forneça um rótulo para o seu volume. É uma prática recomendada para que este nome seja idêntico ao nome de volume que forneceu no seu StorSimple Virtual Array. Clique em **Seguinte**.
    
    ![novo assistente de volume 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Verifique os valores para o seu volume e, em seguida, clique em **Terminar**.
    
    ![novo assistente de volume 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Os volumes aparecerão como **Online** na página de **Gestão de Discos.**
    
    ![volumes online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a usar a UI web local para [administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apêndice A: Obtenha o IQN de um anfitrião do Windows Server

Execute os passos seguintes para obter o Nome Qualificado do iSCSI (IQN) de um anfitrião do Windows que está a executar o Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obter o IQN de um anfitrião do Windows

1. Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Configuração**, selecione e copie a cadeia do campo **Nome do Iniciador**.
   
    ![Propriedades do iniciador iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Guarde esta cadeia.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



