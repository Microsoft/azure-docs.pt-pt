---
title: Microsoft Azure StorSimple Virtual Array iSCSI configuração do servidor / Microsoft Docs
description: Descreve como executar a configuração inicial, registar o seu servidor StorSimple iSCSI e completar a configuração do dispositivo.
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
ms.openlocfilehash: 81799e6ec366c7429fdb29b85b4ff65d353a8fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742422"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Implementar StorSimple Virtual Array – Configurar como um servidor iSCSI através do portal Azure

![fluxo de processo de configuração iscsi](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial de implementação aplica-se ao Microsoft Azure StorSimple Virtual Array. Este tutorial descreve como executar a configuração inicial, registar o seu servidor StorSimple iSCSI, completar a configuração do dispositivo e, em seguida, criar, montar, inicializar e volumes de formato no seu StorSimple Virtual Array configurado como um servidor iSCSI. 

Os procedimentos aqui descritos demoram aproximadamente 30 minutos a 1 hora para ser concluídos. As informações publicadas neste artigo aplicam-se apenas a Arrays Virtuais StorSimple.

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração

Antes de configurar e configurar o seu StorSimple Virtual Array, certifique-se de que:

* Forneceste uma matriz virtual e ligaste-lhe como descrito no [Deploy StorSimple Virtual Array - Provision a virtual array in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Deploy StorSimple Virtual Array - Provision a virtual array in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Tem a chave de registo de serviço do serviço StorSimple Device Manager que criou para gerir as suas Matrizes Virtuais StorSimple. Para mais informações, consulte **o Passo 2: Obtenha a chave de registo de serviço** em Implementar [StorSimple Virtual Array - Prepare o portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Se esta for a segunda matriz virtual ou subsequente que está a registar com um serviço de Gestor de Dispositivos StorSimple existente, deverá ter a chave de encriptação de dados de serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com sucesso com este serviço. Se perdeu esta chave, consulte **Obter a chave de encriptação de dados de serviço** em Utilizar o [UI web para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Configuração passo a passo

Utilize as seguintes instruções passo a passo para configurar e configurar o seu StorSimple Virtual Array:

* [Passo 1: Complete a configuração do UI web local e registe o seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Passo 2: Completar a configuração do dispositivo necessário
* [Passo 3: Adicione um volume](#step-3-add-a-volume)
* [Passo 4: Montar, inicializar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Complete a configuração do UI web local e registe o seu dispositivo

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para completar a configuração e registar o dispositivo

1. Abra uma janela do browser. Para ligar ao tipo web UI:
   
    `https://<ip-address of network interface>`
   
    Utilize o URL de ligação indicado no passo anterior. Verá um erro notificando-o de que há um problema com o certificado de segurança do site. Clique **em Continuar a esta página web.**
   
    ![erro de certificado de segurança](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Inscreva-se na UI web do seu dispositivo virtual como **StorSimpleAdmin**. Introduza a palavra-passe do administrador do dispositivo que alterou no Passo 3: Inicie o dispositivo virtual no [Conjunto Virtual StorSimple de implementação - Provisione um dispositivo virtual em Hiper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [implementar StorSimple Virtual Array - Provisionar um dispositivo virtual em VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Página de inscrição](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Serão levados para a página **inicial.** Esta página descreve as várias definições necessárias para configurar e registar o dispositivo virtual com o serviço StorSimple Device Manager. Note que as **definições de Rede,** **configurações de procuração web**e **definições de tempo** são opcionais. As únicas definições necessárias são **as definições do Dispositivo** e as **definições de Cloud**.
   
    ![Página de boas-vindas](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na página **de definições de Rede** nas **interfaces da Rede,** o DATA 0 será automaticamente configurado para si. Cada interface de rede é definida por padrão para obter um endereço IP automaticamente (DHCP). Portanto, um endereço IP, sub-rede e gateway será automaticamente atribuído (tanto para IPv4 como IPv6).
   
    Como planeia implantar o seu dispositivo como servidor iSCSI (para armazenar blocos), recomendamos que desative automaticamente o **endereço Get IP** e configure endereços IP estáticos.
   
    ![Página de definições de rede](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Se tiver adicionado mais do que uma interface de rede durante o fornecimento do dispositivo, pode configurá-los aqui. Note que pode configurar a sua interface de rede apenas como IPv4 ou como IPv4 e IPv6. As configurações apenas iPv6 não são suportadas.
5. Os servidores DNS são necessários porque são utilizados quando o seu dispositivo tenta comunicar com os seus fornecedores de serviços de armazenamento na nuvem ou para resolver o seu dispositivo pelo nome se este estiver configurado como um servidor de ficheiros. Na página **de definições de rede** nos **servidores DNS**:
   
   1. Um servidor DNS primário e secundário será configurado automaticamente. Se optar por configurar endereços IP estáticos, pode especificar servidores DNS. Para uma elevada disponibilidade, recomendamos que configuure um servidor de DNS primário e secundário.
   2. Clique em **Aplicar**. Isto aplica-se e validará as definições de rede.
6. Na página de definições do **dispositivo:**
   
   1. Atribua um **nome** único ao seu dispositivo. Este nome pode ser de 1 a 15 caracteres e pode conter letras, números e hífenes.
   2. Clique no ícone do servidor **iSCSI** ![ para o tipo de dispositivo que está a ](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) criar. **Type** Um servidor iSCSI permitir-lhe-á providenciar armazenamento de blocos.
   3. Especifique se pretende que este dispositivo seja unido ao domínio. Se o seu dispositivo for um servidor iSCSI, então juntar o domínio é opcional. Se decidir não juntar o servidor iSCSI a um domínio, clique em **Aplicar,** aguarde que as definições sejam aplicadas e, em seguida, salte para o passo seguinte.
      
       Se quiser juntar o dispositivo a um domínio. Introduza um **nome de domínio**e, em seguida, clique em **Aplicar**.
      
      > [!NOTE]
      > Se juntar o seu servidor iSCSI a um domínio, certifique-se de que o seu conjunto virtual está na sua própria unidade organizacional (OU) para o Microsoft Azure Ative Directory e não são aplicados objetos de política de grupo (GPO).
      > 
      > 
   4. Aparecerá uma caixa de diálogo. Introduza as suas credenciais de domínio no formato especificado. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). As credenciais de domínio serão verificadas. Verá uma mensagem de erro se as credenciais estiverem incorretas.
      
       ![credenciais](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Clique em **Aplicar**. Isto aplica-se e valida as definições do dispositivo.
7. (Opcionalmente) configurar o seu servidor de procuração web. Apesar de a configuração do proxy Web ser opcional, tenha em atenção que se utilizar um proxy Web, só pode configurá-lo aqui.
   
    ![configurar o proxy web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na página **de procuração** da Web:
   
   1. Forneça o **URL de procuração web** neste formato: *http: \/ /host-IP address* ou *FQDN:Número de porta*. Note que os URLs HTTPS não são suportados.
   2. Especificar **a autenticação** como **Base** ou **Nenhuma**.
   3. Se estiver a utilizar a autenticação, também terá de fornecer um nome de **utilizador** e **uma palavra-passe.**
   4. Clique em **Aplicar**. Isto validará e aplicará as definições configuradas de procuração web.
8. (Opcionalmente) configurar as definições de tempo para o seu dispositivo, como o fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.
   
    ![Definições de tempo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na página **'Definições tempor':**
   
   1. A partir da lista de espera, selecione o **fuso horário** com base na localização geográfica em que o dispositivo está a ser implantado. O fuso horário padrão para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
   2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor predefinido de time.windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
   3. Especificar opcionalmente um **servidor NTP secundário** para o seu dispositivo.
   4. Clique em **Aplicar**. Isto validará e aplicará as definições de tempo configuradas.
9. Configure as definições de nuvem para o seu dispositivo. Neste passo, irá completar a configuração do dispositivo local e, em seguida, registar o dispositivo com o seu serviço StorSimple Device Manager.
   
   1. Introduza a **chave de registo de Serviço** que obteve no Passo **2: Obtenha a chave de registo de serviço** em Implementar [StorSimple Virtual Array - Prepare o Portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Se este não for o primeiro dispositivo que está a registar com este serviço, terá de fornecer a **chave de encriptação de dados de Serviço**. Esta chave é necessária com a chave de registo de serviço para registar dispositivos adicionais com o serviço StorSimple Device Manager. Para obter mais informações, consulte para [obter a chave de encriptação de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) na sua UI web local.
   3. Clique em **Registar**. Isto reiniciará o dispositivo. Pode ter de esperar 2-3 minutos antes de o aparelho ser registado com sucesso. Depois de o dispositivo ter sido reiniciado, será levado para o sinal na página.
      
      ![Dispositivo de registo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Regresse ao portal do Azure.
11. Navegue para a lâmina **dispositivos** do seu serviço. Se tiver muitos recursos, clique em **Todos os recursos,** clique no nome do seu serviço (procure-o se necessário) e, em seguida, clique em **Dispositivos**.
12. Na lâmina **dispositivos,** verifique se o dispositivo se ligou com sucesso ao serviço, procurando o estado. O estado do dispositivo deve ser **Pronto para configurar**.
    
    ![Dispositivo de implantação](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Passo 2: Configurar o dispositivo como servidor iSCSI

Execute os seguintes passos no portal Azure para completar a configuração do dispositivo necessária.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Para configurar o dispositivo como servidor iSCSI

1. Vá ao seu serviço StorSimple Device Manager e, em seguida, vá a **Dispositivos de > de Gestão**. Na lâmina **dispositivos,** selecione o dispositivo que acabou de criar. Este dispositivo apareceria como **Pronto para configurar**.
   
    ![Dispositivo de configuração como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Clique no dispositivo e verá uma mensagem de banner indicando que o dispositivo está pronto para ser configurado.
   
    ![Dispositivo de configuração como servidor iSCSI 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Clique em **Configurar** na barra de comando do dispositivo. Isto abre a lâmina **de configuração.** Na lâmina **configurar,** faça o seguinte:
   
   * O nome do servidor iSCSI é automaticamente preenchido.
   * Certifique-se de que a encriptação de armazenamento em nuvem está definida para **Ativação**. Isto garante que os dados enviados do dispositivo para a nuvem são encriptados.
   * Especifique uma chave de encriptação de 32 caracteres e grave-a numa aplicação de gestão chave para referência futura.
   * Selecione uma conta de armazenamento para ser utilizada com o seu dispositivo. Nesta subscrição, pode selecionar uma conta de armazenamento existente, ou pode clicar em **Adicionar** para escolher uma conta a partir de uma subscrição diferente.
     
     ![Dispositivo de configuração como servidor iSCSI 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Clique **em Configurar** para concluir a configuração do servidor iSCSI.
   
    ![Dispositivo de configuração como servidor iSCSI 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Será notificado de que a criação do servidor iSCSI está em curso. Após a criação do servidor iSCSI com sucesso, a lâmina **do Dispositivo** é atualizada e o estado do dispositivo correspondente está **online**.
   
    ![Dispositivo de configuração como servidor iSCSI 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Passo 3: Adicione um volume

1. Na lâmina **dispositivos,** selecione o dispositivo que acabou de configurar como um servidor iSCSI. Clique **...** (clique alternativamente à direita nesta linha) e a partir do menu de contexto, selecione **Adicionar volume**. Também pode clicar **+ Adicionar volume** a partir da barra de comando. Isto abre a lâmina **de volume adicionar.**
   
    ![Adicione um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Na lâmina **de volume adicionar,** faça o seguinte:
   
   * No campo **de nomes Volume,** insira um nome único para o seu volume. O nome deve ser uma corda que contenha 3 a 127 caracteres.
   * Na lista de dropdown **do tipo,** especifique se deve criar um volume **tiered** ou **localmente fixado.** Para cargas de trabalho que exijam garantias locais, baixas latências e maior desempenho, selecione **volume** **fixo local.** Para todos os outros dados, **selecione Volume Tiered** **volume**.
   * No campo **Capacidade,** especifique o tamanho do volume. Um volume hierárquico deve estar entre 500 GB e 5 TB e um volume fixado localmente deve estar entre 50 GB e 500 GB.
     
     Um volume fixado localmente é densamente a provisionado e garante que os dados primários no volume permanecem no dispositivo e não derramem para a nuvem.
     
     Por outro lado, é pouco abastetado um volume hierárquico. Quando se cria um volume hierárquico, aproximadamente 10% do espaço é a provisionado no nível local e 90% do espaço é a provisionado na nuvem. Por exemplo, se fornecesse um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seriam usados na nuvem quando os níveis de dados. Isto, por sua vez, implica que se ficar sem todo o espaço local do dispositivo, não pode disponibilizar uma parte diferenciada (porque os 10% não estarão disponíveis).
     
     ![Adicione um volume 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Clique **em Anfitriões Conectados**, selecione um registo de controlo de acesso (ACR) correspondente ao iniciador iSCSI que pretende ligar a este volume e, em seguida, clique em **Select**. <br><br> 
3. Para adicionar um novo anfitrião conectado, clique em **Adicionar novo,** insira um nome para o anfitrião e o seu nome qualificado iSCSI (IQN) e, em seguida, clique em **Adicionar**. Se não tiver o IQN, vá ao [Apêndice A: Obtenha o IQN de um anfitrião do Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Adicione um volume 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Quando terminar de configurar o seu volume, clique **em OK**. Será criado um volume com as definições especificadas e verá uma notificação. Por predefinição, a monitorização e a cópia de segurança serão ativadas para o volume.
   
     ![Adicione um volume 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Para confirmar que o volume foi criado com sucesso, vá para a lâmina **Volumes.** Devia ver o volume listado.
   
   ![Adicione um volume 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Passo 4: Montar, inicializar e formatar um volume

Execute os seguintes passos para montar, inicializar e formatar os seus volumes StorSimple num anfitrião do Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar e formatar um volume

1. Abra a aplicação **de iniciador iSCSI** no servidor apropriado.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Deteção**, clique em **Detetar Portal**.
   
    ![descobrir portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. Na caixa de diálogo **Detetar Portal de Destino**, forneça o endereço IP da sua interface de rede ativada com permissão para iSCSI e clique em **OK**.
   
    ![Endereço IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Na janela **Propriedades do Iniciador iSCSI**, no separador **Destinos**, localize os **Destinos Detetados**. (Cada volume será um alvo descoberto.) O estado do dispositivo deve aparecer como **Inativo**.
   
    ![alvos descobertos](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Selecione um dispositivo alvo e, em seguida, clique em **Connect**. Quando o dispositivo estiver ligado, o estado deverá mudar para **Ligado**. (Para obter mais informações sobre a utilização do iniciador Microsoft iSCSI, consulte [instalar e configurar o Iniciador iSCSI da Microsoft][1].
   
    ![selecionar dispositivo-alvo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. No anfitrião do Windows, prima a tecla do Logótipo do Windows + X e clique em **Executar**.
7. Na caixa de diálogo **Executar**, escreva **Diskmgmt.msc**. Clique em **OK**. Será apresentada a caixa de diálogo **Gestão de Discos**. O painel da direita apresentará os volumes no anfitrião.
8. Na janela **Gestão de Discos**, os volumes montados serão apresentados como mostrado na ilustração seguinte. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.
   
    ![gestão de discos](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Clique à direita e **selecione Inicialize O Disco**.
   
    ![inicializar o disco 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Na caixa de diálogo, selecione o disco(s) para inicializar e, em seguida, clique em **OK**.
    
    ![inicializar o disco 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Começa o novo assistente de volume simples. Selecione um tamanho de disco e, em seguida, clique em **Seguinte**.
    
    ![novo assistente de volume 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Atribua uma letra de unidade ao volume e, em seguida, clique em **Seguinte**.
    
    ![novo assistente de volume 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Introduza os parâmetros para formatar o volume. **No Windows Server, apenas o NTFS é suportado.** Desloque o tamanho da unidade de atribuição para 64K. Forneça uma etiqueta para o seu volume. É uma boa prática recomendada para que este nome seja idêntico ao nome de volume que forneceu no seu StorSimple Virtual Array. Clique em **Seguinte**.
    
    ![novo assistente de volume 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Verifique os valores do seu volume e, em seguida, clique em **Terminar**.
    
    ![novo assistente de volume 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Os volumes aparecerão como **Online** na página **de Gestão** de Discos.
    
    ![volumes on-line](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar a UI web local para [administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apêndice A: Obtenha o IQN de um anfitrião do Windows Server

Execute os passos seguintes para obter o Nome Qualificado do iSCSI (IQN) de um anfitrião do Windows que está a executar o Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obter o IQN de um anfitrião do Windows

1. Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Configuração**, selecione e copie a cadeia do campo **Nome do Iniciador**.
   
    ![Propriedades do iniciador iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Guarde esta cadeia.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



