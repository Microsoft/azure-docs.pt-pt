---
title: Configurar o StorSimple Virtual Array como servidor de ficheiros Microsoft Docs
description: Este terceiro tutorial na implementação do Array Virtual StorSimple instrui-o a configurar um dispositivo virtual como servidor de ficheiros.
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
ms.openlocfilehash: 244fdbf7cb723fe85e0987d176a13242f0bff064
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005933"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Implementar StorSimple Virtual Array - Configurar como servidor de ficheiros através do portal Azure
![Diagrama mostrando os passos necessários para implantar uma matriz virtual. A primeira parte do terceiro passo é rotulada Configurar como servidor de ficheiros e é realçada.](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introdução

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este artigo descreve como executar a configuração inicial, registar o seu servidor de ficheiroSSimple, completar a configuração do dispositivo e criar e ligar-se às ações do SMB. Este é o último artigo da série de tutoriais de implementação necessários para implementar completamente o seu array virtual como um servidor de ficheiros ou um servidor iSCSI.

O processo de configuração e configuração pode demorar cerca de 10 minutos a ser concluído. As informações neste artigo aplicam-se apenas à implantação do Array Virtual StorSimple. Para a implementação de dispositivos da série StorSimple 8000, vá para: [Implemente o seu dispositivo da série StorSimple 8000 em execução Update 2](./storsimple-8000-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração
Antes de configurar e configurar o seu StorSimple Virtual Array, certifique-se de que:

* Você forneceu uma matriz virtual e ligou-lhe conforme detalhado na [Provisão de uma Matriz Virtual StorSimple em Hiper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [Provision a StorSimple Virtual Array em VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Tem a chave de registo de serviço do serviço StorSimple Device Manager que criou para gerir as Matrizes Virtuais StorSimple. Para mais informações, consulte [o Passo 2: Obtenha a chave de registo de serviço](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para StorSimple Virtual Array.
* Se esta for a segunda matriz virtual ou subsequente que está a registar com um serviço de Gestor de Dispositivos StorSimple existente, deverá ter a chave de encriptação de dados de serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com sucesso com este serviço. Se perdeu esta chave, consulte [obter a chave de encriptação de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) para o seu StorSimple Virtual Array.

## <a name="step-by-step-setup"></a>Configuração passo a passo
Utilize as seguintes instruções passo a passo para configurar e configurar o seu StorSimple Virtual Array.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Complete a configuração do UI web local e registe o seu dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Para completar a configuração e registar o dispositivo
1. Abra uma janela do navegador e ligue-se à UI web local. Escreva:
   
   `https://<ip-address of network interface>`
   
   Utilize o URL de ligação indicado no passo anterior. Vê um erro que indica que há um problema com o certificado de segurança do site. Clique **em Continuar a esta página web.**
   
   ![Screenshot de uma janela do navegador reportando um problema de certificado de segurança. São visíveis dois links, um para o website e outro para a página inicial do utilizador.](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Inscreva-se na UI web da sua matriz virtual como **StorSimpleAdmin**. Introduza a palavra-passe do administrador do dispositivo que alterou no Passo 3: Inicie a matriz virtual em [Provision a StorSimple Virtual Array em Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou em [Provision a StorSimple Virtual Array em VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![Screenshot da página de storSimple de sinsutor. O nome de utilizador StorSimpleAdmin é visível e uma caixa de palavra-passe é preenchida com caracteres indeterminados.](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Foi levado para a página **inicial.** Esta página descreve as várias definições necessárias para configurar e registar a matriz virtual com o serviço StorSimple Device Manager. As **definições de Rede,** **as definições de procuração web** e as **definições de tempo** são opcionais. As únicas definições necessárias são **as definições do Dispositivo** e as **definições de Cloud**.
   
   ![Screenshot da página inicial. O texto indica que o dispositivo não está configurado. São visíveis ligações a vários tipos diferentes de configurações.](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Na página **de definições de Rede** nas **interfaces da Rede,** o DATA 0 será automaticamente configurado para si. Cada interface de rede é definida por padrão para obter o endereço IP automaticamente (DHCP). Assim, um endereço IP, sub-rede e gateway são automaticamente atribuídos (tanto para IPv4 como IPv6).
   
   ![Screenshot da página de definições de rede mostrando os endereços IP configurados para várias versões do Protocolo de Internet.](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Se tiver adicionado mais do que uma interface de rede durante o fornecimento do dispositivo, pode configurá-los aqui. Note que pode configurar a sua interface de rede apenas como IPv4 ou como IPv4 e IPv6. As configurações apenas iPv6 não são suportadas.
5. Os servidores DNS são necessários porque são utilizados quando o seu dispositivo tenta comunicar com os seus fornecedores de serviços de armazenamento na nuvem ou para resolver o seu dispositivo pelo nome quando configurado como um servidor de ficheiros. Na página **de definições de Rede** nos **servidores DNS**:
   
   1. Um servidor DNS primário e secundário é configurado automaticamente. Se optar por configurar endereços IP estáticos, pode especificar servidores DNS. Para uma elevada disponibilidade, recomendamos que configuure um servidor de DNS primário e secundário.
   2. Clique **em Aplicar** para aplicar e validar as definições de rede.
6. Na página de definições do **dispositivo:**
   
   1. Atribua um **nome** único ao seu dispositivo. Este nome pode ser de 1 a 15 caracteres e pode conter letras, números e hífenes.
   2. Clique no ícone **do servidor de ficheiro** para o :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image6.png"::: **tipo** de dispositivo que está a criar. Um servidor de ficheiros permitir-lhe-á criar pastas partilhadas.
   3. Como o seu dispositivo é um servidor de ficheiros, terá de se juntar ao dispositivo a um domínio. Introduza um **nome de domínio**.
   4. Clique em **Aplicar**.
7. Aparecerá uma caixa de diálogo. Introduza as suas credenciais de domínio no formato especificado. Clique no ícone de verificação. As credenciais de domínio são verificadas. Vê uma mensagem de erro se as credenciais estiverem incorretas.
   
   ![Screenshot mostrando uma caixa de diálogo com o nome de utilizador e senha preenchidas.](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Clique em **Aplicar**. Isto aplica-se e valida as definições do dispositivo.
   
   ![Screenshot da página de definições do dispositivo. O nome do dispositivo e o nome de domínio estão preenchidos.](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e que nenhum objeto de política de grupo (GPO) lhe é aplicado ou herdado. A política do grupo pode instalar aplicações como software antivírus no StorSimple Virtual Array. A instalação de software adicional não é suportada e pode levar à corrupção de dados. 
   > 
   > 
9. (Opcionalmente) configurar o seu servidor de procuração web. Apesar de a configuração do proxy Web ser opcional, tenha em atenção que se utilizar um proxy Web, só pode configurá-lo aqui.
   
   ![Screenshot da página de configurações de procuração web. A opção Ativa proxy web está desligada e a autenticação está definida para Nenhum. Não estão definidos outros valores.](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Na página **de procuração** da Web:
   
   1. Forneça o **URL de procuração web** neste formato: http:// endereço DE *&lt; ANFITRIÃO-IP ou &gt; número de :P ort da FQDN*. Note que os URLs HTTPS não são suportados.
   2. Especificar **a autenticação** como **Base** ou **Nenhuma**.
   3. Se utilizar a autenticação, também terá de fornecer um nome de **utilizador** e **uma palavra-passe.**
   4. Clique em **Aplicar**. Isto validará e aplicará as definições configuradas de procuração web.
10. (Opcionalmente) configurar as definições de tempo para o seu dispositivo, como o fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.
    
    ![Screenshot da página de definições de tempo. O fuso horário e o servidor N T P primário estão preenchidos. O servidor secundário N T P está em branco.](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Na página **de definições de tempo:**
    
    1. A partir da lista de dropdown, selecione o **fuso horário** com base na localização geográfica em que o dispositivo está a ser implantado. O fuso horário padrão para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
    2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor predefinido de time.windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
    3. Especificar opcionalmente um **servidor NTP secundário** para o seu dispositivo.
    4. Clique em **Aplicar**. Isto validará e aplicará as definições de tempo configuradas.
11. Configure as definições de nuvem para o seu dispositivo. Neste passo, irá completar a configuração do dispositivo local e, em seguida, registar o dispositivo com o seu serviço StorSimple Device Manager.
    
    1. Insira a **chave de registo de Serviço** que obteve no Passo [2: Obtenha a chave de registo de serviço](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para StorSimple Virtual Array.
    2. Se este for o seu primeiro dispositivo a registar-se com este serviço, será apresentado com a **chave de encriptação de dados de Serviço**. Copie esta chave e guarde-a numa localização segura. Esta chave é necessária com a chave de registo de serviço para registar dispositivos adicionais com o serviço StorSimple Device Manager. 
       
       Se este não for o primeiro dispositivo que está a registar com este serviço, terá de fornecer a chave de encriptação de dados de serviço. Para obter mais informações, consulte para obter a [chave de encriptação de dados](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) de serviço na sua UI web local.
    3. Clique em **Registar**. Isto reiniciará o dispositivo. Pode ter de esperar 2-3 minutos antes de o aparelho ser registado com sucesso. Depois de o dispositivo ter sido reiniciado, será levado para o sinal na página.
       
       ![Screenshot da página de definições cloud. A chave de registo e as caixas-chave de encriptação estão preenchidas, mas os valores são redigidos.](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Regresse ao portal do Azure. Vá a **Todos os recursos,** procure o seu serviço StorSimple Device Manager.
    
    ![Screenshot da página de todos os recursos do portal Azure. Destaca-se um serviço de Gestor de Dispositivos.](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na lista filtrada, selecione o seu serviço StorSimple Device Manager e, em seguida, navegue para **Dispositivos de Gestão >**. Na lâmina **dispositivos,** verifique se o dispositivo se ligou com sucesso ao serviço e tem o estado **pronto a configurar**.
    
    ![Implementar](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Passo 2: Configurar o dispositivo como servidor de ficheiros
Execute os seguintes passos no [portal Azure](https://portal.azure.com/) para completar a configuração do dispositivo necessária.

#### <a name="to-configure-the-device-as-file-server"></a>Para configurar o dispositivo como servidor de ficheiros
1. Vá ao seu serviço StorSimple Device Manager e, em seguida, vá a  **Dispositivos de > de Gestão**. Na lâmina **dispositivos,** selecione o dispositivo que acabou de criar. Este dispositivo apareceria como **Pronto para configurar**.
   
   ![Configure um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Clique no dispositivo e verá uma mensagem de banner indicando que o dispositivo está pronto para ser configurado.
   
    ![Configure um servidor de ficheiros 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Clique em **Configurar** na barra de comando. Isto abre a lâmina **de configuração.** Na lâmina **configurar,** faça o seguinte:
   
   1. O nome do servidor de ficheiros é automaticamente preenchido.
    
   2. Certifique-se de que a encriptação de armazenamento em nuvem está definida para **Ativação**. Isto irá encriptar todos os dados que são enviados para a nuvem. 
    
   3. Uma chave AES de 256 bits é utilizada com a chave definida pelo utilizador para encriptação. Especifique uma chave de 32 caracteres e, em seguida, reentre na chave para confirmá-la. Grave a chave numa aplicação de gestão chave para referência futura.
    
   4. Clique **em Configurar as definições necessárias** para especificar as credenciais de conta de armazenamento a serem usadas com o seu dispositivo. Clique **em Adicionar novos** se não houver credenciais de conta de armazenamento configuradas. **Certifique-se de que a conta de armazenamento que utiliza suporta bolhas de bloco. As bolhas de página não são suportadas.** Mais informações sobre [blocos blobs e bolhas de página](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Configure um servidor de ficheiros 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Na lâmina de credenciais de **conta de armazenamento Adicionar,** faça o seguinte: 

    1. Escolha a subscrição atual se a conta de armazenamento estiver na mesma subscrição que o serviço. Especificar outro é que a conta de armazenamento está fora da subscrição de serviço. 
    
    2. A partir da lista de dropdown, escolha uma conta de armazenamento existente. 
    
    3. A localização será automaticamente povoada com base na conta de armazenamento especificada. 
    
    4. Ativar o TLS para garantir um canal de comunicação de rede seguro entre o dispositivo e a nuvem.
    
    5. Clique **em Adicionar** para adicionar esta credencial de conta de armazenamento. 
   
        ![Configurar um servidor de ficheiros 4](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Uma vez criada a credencial da conta de armazenamento com sucesso, a lâmina **de configuração** será atualizada para exibir as credenciais de conta de armazenamento especificadas. Clique em **Configurar**.
   
   ![Configurar um servidor de ficheiros 5](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Verá que um servidor de ficheiros está a ser criado. Assim que o servidor de ficheiros for criado com sucesso, será notificado.
   
   ![Configure um servidor de ficheiros 5b](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   O estado do dispositivo também será alterado para **Online**.
   
   ![Configurar um servidor de ficheiros 5c](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Pode proceder para adicionar uma parte.

## <a name="step-3-add-a-share"></a>Passo 3: Adicionar uma parte
Execute os seguintes passos no [portal do Azure](https://portal.azure.com/) para criar uma partilha.

#### <a name="to-create-a-share"></a>Para criar uma parte
1. Selecione o dispositivo do servidor de ficheiros configurado no passo anterior e clique **em ...** (ou clique à direita). No menu de contexto, **selecione Adicionar partilha**. Alternativamente, pode clicar **+ Adicionar Partilhar** na barra de comando do dispositivo.
   
   ![Adicionar uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Especificar as seguintes definições de ações:

   1. Um nome único para a sua parte. O nome deve ser uma corda que contenha 3 a 127 caracteres.
    
   2. Uma **descrição** opcional para a parte. A descrição ajudará a identificar os proprietários de ações.
    
   3. Um **tipo** para a parte. O tipo pode ser **Tiered** ou **Localmente fixado,** sendo o padrão hierárquico. Para cargas de trabalho que requerem garantias locais, baixas latências e maior desempenho, selecione uma quota **fixa local.** Para todos os outros dados, selecione uma partilha **Tiered.**
      Uma parte fixa local é densamente a provisionada e garante que os dados primários sobre a partilha permanecem locais para o dispositivo e não derramem para a nuvem. Por outro lado, uma parte diferenciada é pouco abastecada. Quando se cria uma quota hierárquica, 10% do espaço é a provisionado no nível local e 90% do espaço é a provisionado na nuvem. Por exemplo, se fornecesse um volume de 1 TB, 100 GB residiria no espaço local e 900 GB seriam usados na nuvem quando os níveis de dados. Isto, por sua vez, implica que se ficar sem todo o espaço local do dispositivo, não poderá providenciar uma parte diferenciada.
   
   4. Nas **permissões completas do Conjunto para** o campo, atribua as permissões ao utilizador ou ao grupo que está a aceder a esta partilha. Especifique o nome do utilizador ou do grupo de utilizador no formato *John \@ contoso.com.* Recomendamos que utilize um grupo de utilizadores (em vez de um único utilizador) para permitir que os privilégios de administração acedam a estas partilhas. Depois de ter atribuído as permissões aqui, pode utilizar o Explorador de Ficheiros para modificar estas permissões.
   
   5. Clique **em Adicionar** para criar a partilha. 
    
       ![Adicione uma parte 1](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Será notificado de que a criação da partilha está em curso.
   
       ![Adicione uma ação 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Após a criação da partilha com as definições especificadas, a lâmina **de Partilhas** atualizar-se-á para refletir a nova ação. Por predefinição, a monitorização e a cópia de segurança estão ativadas para a partilha.
   
      ![Adicione uma ação 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Passo 4: Ligar-se à parte
Terá agora de se ligar a uma ou mais ações que criou no passo anterior. Execute estes passos no seu anfitrião do Windows Server ligado ao seu StorSimple Virtual Array.

#### <a name="to-connect-to-the-share"></a>Para ligar à parte
1. Prima :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image22.png"::: + R. Na janela Executar, especifique o *&lt; &gt; nome do servidor de ficheiros&#92;&#92;* como o caminho, substituindo o nome do *servidor de ficheiros* pelo nome do dispositivo que atribuiu ao seu servidor de ficheiros. Clique em **OK**.
   
   ![Screenshot da caixa de diálogo Run. A caixa Open é preenchida com um caminho que conduz ao servidor de ficheiros.](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Isto abre o File Explorer. Agora deve conseguir ver as partilhas que criou como pastas. Selecione e faça duplo clique numa partilha (pasta) para ver o conteúdo.
   
   ![Screenshot do Explorador de Ficheiros. São visíveis várias pastas que representam as ações recém-criadas.](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Agora pode adicionar ficheiros a estas ações e fazer uma cópia de segurança.

## <a name="next-steps"></a>Passos seguintes
Saiba como utilizar a UI web local para [administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).