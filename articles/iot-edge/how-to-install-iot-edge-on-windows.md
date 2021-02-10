---
title: Instale a borda Azure IoT para Linux no Windows | Microsoft Docs
description: Instruções de instalação do Azure IoT Edge em dispositivos Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 049c24beb6bb1573458779bf0796357fa634898f
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008575"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Instalação e fornecimento Azure IoT Edge para Linux num dispositivo Windows (Pré-visualização)

O tempo de execução Azure IoT Edge é o que transforma um dispositivo num dispositivo IoT Edge. O tempo de funcionaamento pode ser implementado em dispositivos da classe PC para servidores industriais. Quando um dispositivo é configurado com o runtime do IoT Edge, pode começar a implementar a lógica de negócio no mesmo partir da cloud. Para saber mais, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

O Azure IoT Edge para Linux no Windows permite-lhe utilizar o Azure IoT Edge em dispositivos Windows utilizando máquinas virtuais Linux. A versão Linux do Azure IoT Edge e quaisquer módulos Linux implantados com ele funcionam na máquina virtual. A partir daí, as aplicações e códigos do Windows e o tempo de execução e módulos IoT Edge podem interagir livremente entre si.

Este artigo lista os passos para configurar o IoT Edge num dispositivo Windows. Estes passos implementam uma máquina virtual Linux que contém o tempo de execução do IoT Edge para ser executado no seu dispositivo Windows e, em seguida, forrou o dispositivo com a identidade do dispositivo IoT Hub.

>[!NOTE]
>IoT Edge for Linux on Windows está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Embora o IoT Edge para o Linux no Windows seja a experiência recomendada para a utilização do Azure IoT Edge num ambiente Windows, os contentores windows ainda estão disponíveis. Se preferir utilizar recipientes Windows, consulte o guia de como [instalar e gerir o Azure IoT Edge para windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição válida. Se não tiver uma [subscrição do Azure,](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de nível gratuito ou padrão em Azure.

* Um dispositivo Windows com os seguintes requisitos mínimos do sistema:

  * Versão 1809 ou posterior do Windows 10; construir 17763 ou mais tarde
  * Edições profissionais, empresariais ou servidoras
  * RAM mínimo: 4 GB (8 GB recomendado)
  * Armazenamento mínimo: 10 GB

* Acesso ao Windows Admin Center insider build com a extensão Azure IoT Edge para Windows Admin Center instalado:  <!-- The link below needs the language localization to work; otherwise broken -->
   1. Visite a [pré-visualização](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewserver)do Windows Insider .

   1. No dropdown das pré-visualizações, selecione **Windows Admin Center Preview - Build 2012**, e escolha **Confirmar**.

      ![Escolha a pré-visualização do Windows Admin Center - Construa 2012 a partir do menu suspenso das pré-visualizações disponíveis.](./media/how-to-install-iot-edge-on-windows/select-windows-admin-center-preview-build.png)

   1. No **dropdown de língua select,** escolha **inglês** e escolha **Confirmar**.

   1. Escolha **baixar agora** para baixar o *WindowsAdminCenterPreview2012.msi*.

   1. Executar o *WindowsAdminCenterPreview2012.msi* e seguir as instruções do assistente de instalação para instalar o Windows Admin Center. Uma vez instalado, abra o Windows Admin Center.

   1. Na primeira utilização do Windows Admin Center, será solicitado que selecione um certificado para utilizar. Selecione **o Cliente do Centro de Administração do Windows** como o seu certificado.

   1. É hora de instalar a extensão Azure IoT Edge. Selecione o ícone de engrenagem no topo direito do painel do Windows Admin Center.

      ![Selecione o ícone de engrenagem no topo direito do painel para aceder às definições.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. No menu **Definições,** em **Gateway,** selecione **Extensões**.

   1. Selecione o **separador Feeds** e selecione **Adicionar**.

   1. https://aka.ms/wac-insiders-feedIntroduza na caixa de texto e selecione **Adicionar**.

   1. Depois de adicionar o feed, navegue para o separador **extensões Disponível.** Pode levar um momento para atualizar a lista de extensões.

   1. No separador **extensões disponíveis,** encontre **a Azure IoT Edge** na lista de extensões. Escolha-o e selecione o pedido **de instalação** acima da lista de extensões.

   1. Após a conclusão da instalação, deverá ver a Azure IoT Edge na lista de extensões instaladas no separador **extensões instalados.**

## <a name="choose-your-provisioning-method"></a>Escolha o seu método de provisionamento

A azure IoT Edge for Linux on Windows suporta os seguintes métodos de provisionamento:

* Provisão manual utilizando a cadeia de ligação do seu dispositivo IoT Edge. Para utilizar este método, registe o seu dispositivo e recupere uma cadeia de ligação utilizando os passos [do Registo de um dispositivo IoT Edge no IoT Hub](how-to-register-device.md).
  * Escolha a opção de autenticação chave simétrica, uma vez que os certificados auto-assinados X.509 não são atualmente suportados pelo IoT Edge para o Linux no Windows.
* Fornecimento automático utilizando o Serviço de Provisionamento de Dispositivos (DPS) e as teclas simétricas. Saiba mais sobre [a criação e a provisionamento de um dispositivo IoT Edge com DPS e teclas simétricas](how-to-auto-provision-symmetric-keys.md).
* Provisionamento automático utilizando certificados DPS e X.509. Saiba mais sobre [a criação e a provisionação de um dispositivo IoT Edge com certificados DPS e X.509](how-to-auto-provision-x509-certs.md).

O provisionamento manual é mais fácil de começar com alguns dispositivos. O Serviço de Provisionamento de Dispositivos é útil para o fornecimento de muitos dispositivos.

Se planeia utilizar um dos métodos DPS para forrar o seu dispositivo ou dispositivos, siga os passos do artigo apropriado acima ligado para criar uma instância de DPS, ligue a sua instância de DPS ao seu IoT Hub e crie uma inscrição de DPS. Pode criar uma *inscrição individual* para um único dispositivo ou uma *inscrição em grupo* para um grupo de dispositivos. Para obter mais informações sobre os tipos de inscrição, visite os conceitos do [Serviço de Provisionamento de Dispositivos Azure IoT Hub](https://docs.microsoft.com/azure/iot-dps/concepts-service#enrollment).

## <a name="create-a-new-deployment"></a>Criar uma nova implantação

Crie a sua implementação de Azure IoT Edge para Linux no Windows no seu dispositivo-alvo.

# <a name="windows-admin-center"></a>[Centro de Administração windows](#tab/windowsadmincenter)

Na página inicial do Windows Admin Center, na lista de ligações, verá uma ligação de anfitrião local representando o PC onde executa o Windows Admin Center. Quaisquer servidores adicionais, computadores ou clusters que gere também aparecerão aqui.

Pode utilizar o Windows Admin Center para escamar e gerir o Azure IoT Edge para o Linux no Windows, quer no seu dispositivo local, quer em dispositivos geridos à distância. Neste guia, a ligação local ao anfitrião servirá como o dispositivo alvo para a implementação do Azure IoT Edge para o Linux no Windows.

Se pretender implantar-se num dispositivo-alvo remoto em vez do dispositivo local e não vir o dispositivo alvo pretendido na lista, siga as [instruções para adicionar](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)o seu dispositivo.

   ![Painel inicial do Centro de Administração do Windows com dispositivo-alvo listado](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Selecione **Adicionar**.

1. No painel **de recursos Add ou create resources,** localize o azulejo **Azure IoT Edge.** **Selecione Criar novos** para instalar uma nova instância de Azure IoT Edge para Linux no Windows num dispositivo.

   Se já tiver IoT Edge para Linux no Windows em execução num dispositivo, pode selecionar **Adicionar** para ligar ao dispositivo IoT Edge existente e geri-lo com o Windows Admin Center.

   ![Selecione Criar Novo em azulejo Azure IoT Edge no Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Será aberto **o Azure IoT Edge para Linux no painel de implantação do Windows.** No **1. Obter o** separador Iniciar, verifique se o seu dispositivo-alvo cumpre os requisitos mínimos e selecione **Next**.

1. Reveja os termos da licença, verifique **I Accept,** e selecione **Next**.

1. Pode alternar **dados de diagnóstico opcionais** dentro ou fora, dependendo da sua preferência.

1. Selecione **Seguinte: Implementar**.

   ![Selecione o Seguinte: Implementar o botão depois de carregar os dados de diagnóstico opcionais à sua preferência](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. No **2. Implementar** separador, em **Selecionar um dispositivo-alvo,** clique no seu dispositivo listado para validá-lo cumpre os requisitos mínimos. Uma vez confirmado o seu estado como suportado, selecione **Next**.

   ![Selecione o seu dispositivo para verificar se está suportado](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Aceite as definições predefinidos no separador **2.2 Definições.**

1. No **separador 2.3 De implementação,** pode observar o progresso da implantação. O processo completo inclui o download do Azure IoT Edge para Linux no pacote Windows, a instalação do pacote, a configuração do dispositivo anfitrião e a instalação da máquina virtual Linux. Este processo pode demorar vários minutos a ser concluído. Uma implementação bem sucedida é retratada abaixo.

   ![Uma implementação bem sucedida mostrará cada passo com uma marca de verificação verde e uma etiqueta 'Complete'](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Uma vez concluída a sua implantação, está pronto para a provisionar o seu dispositivo. Selecione **Seguinte: Ligue-se** para avançar para o **3. Ligue** o separador, que trata do fornecimento do dispositivo Azure IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Instale o IoT Edge para o Linux no Windows no seu dispositivo-alvo se ainda não o tiver feito.

> [!NOTE]
> O processo PowerShell seguinte descreve como criar uma implementação local do Azure IoT Edge para Linux no Windows. Para criar uma implementação para um dispositivo alvo remoto utilizando o PowerShell, pode utilizar [o Remote PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_remote) para estabelecer uma ligação a um dispositivo remoto e executar estes comandos remotamente nesse dispositivo.

1. Numa sessão elevada do PowerShell, execute cada um dos seguintes comandos para descarregar IoT Edge para Linux no Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzureEdgeForLinuxOnWindowsMSI" -OutFile $msiPath
   ```

1. Instale ioT Edge para Linux no Windows no seu dispositivo.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Pode especificar o IoT Edge personalizado para Linux na instalação do Windows e diretórios VHDX adicionando os parâmetros INSTALLDIR="<FULLY_QUALIFIED_PATH>" e VHDXDIR="<FULLY_QUALIFIED_PATH>" ao comando de instalação acima.

1. Para que a implementação seja executada com sucesso, é necessário definir a política de execução no dispositivo-alvo `AllSigned` para, se ainda não estiver. Pode verificar a política de execução atual num pedido elevado do PowerShell utilizando:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Se a política de execução não for, pode definir a política de `local machine` `AllSigned` execução utilizando:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Crie a Borda IoT para Linux na implementação do Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   <!-- Most likely temporary until cmdlet is fully documented -->
   > [!NOTE]
   > Pode executar este comando sem parâmetros ou personalizar opcionalmente a implementação com parâmetros. Inspecione o módulo PowerShell AzureEFLOW.psm1 para ver os parâmetros e o seu significado (ver em C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW).

1. Insira 'Y' para aceitar os termos da licença.

1. Introduza 'O' ou 'R' para alternar **dados de diagnóstico opcionais** dentro ou fora, dependendo da sua preferência. Uma implementação bem sucedida é retratada abaixo.

   ![Uma implementação bem sucedida dirá "Implementação bem sucedida" no final das mensagens](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Uma vez concluída a sua implantação, está pronto para a provisionar o seu dispositivo.

---

Para o fornecimento do seu dispositivo, pode seguir os links abaixo para saltar para a secção para o seu método de provisionamento selecionado:

* [Opção 1: Provisão manual utilizando a cadeia de ligação do seu dispositivo IoT Edge](#option-1-provisioning-manually-using-the-connection-string)
* [Opção 2: Provisão automática utilizando o Serviço de Provisionamento de Dispositivos (DPS) e chaves simétricas](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Opção 3: Provisão automática utilizando certificados DPS e X.509](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>A provisione o seu dispositivo

Escolha um método para a provisionar o seu dispositivo e siga as instruções na secção apropriada. Pode utilizar o Windows Admin Center ou uma sessão PowerShell elevada para forragem dos seus dispositivos.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Opção 1: Provisionamento manual mente utilizando a cadeia de ligação

Esta secção cobre o fornecimento manual do seu dispositivo utilizando a cadeia de ligação do seu dispositivo Azure IoT Edge.

# <a name="windows-admin-center"></a>[Centro de Administração windows](#tab/windowsadmincenter)

1. No **painel de provisionamento do dispositivo Azure IoT Edge,** selecione **Connection String (Manual)** a partir do método de provisionamento.

1. No [portal Azure,](https://ms.portal.azure.com/)navegue para o **separador IoT Edge** do seu Hub IoT.

1. Clique no ID do dispositivo do seu dispositivo. Copie o campo **de cordas de ligação primária.**

1. Cole-o no campo de cordas de ligação do dispositivo no Windows Admin Center. Em seguida, escolha **Provisioning com o método selecionado.**

   ![Escolha o provisionamento com o método selecionado depois de colar a cadeia de ligação do seu dispositivo](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Uma vez que o provisionamento esteja concluído, **selecione Terminar**. Serão levados de volta para o painel principal. Agora, deve ver um novo dispositivo listado, cujo tipo é `IoT Edge Devices` . Pode selecionar o dispositivo IoT Edge para se ligar ao mesmo. Uma vez na sua página **de visão geral,** pode ver a Lista de **Módulos IoT Edge** e o Estado da **Borda IoT** do seu dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. No [portal Azure,](https://ms.portal.azure.com/)navegue para o **separador IoT Edge** do seu Hub IoT.

1. Clique no ID do dispositivo do seu dispositivo. Copie o campo **de cordas de ligação primária.**

1. Cole o texto do espaço reservado no comando seguinte e execute-o numa sessão PowerShell elevada no seu dispositivo-alvo.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Opção 2: Provisionamento via DPS utilizando chaves simétricas

Esta secção cobre o fornecimento do seu dispositivo automaticamente utilizando dPS e teclas simétricas.

# <a name="windows-admin-center"></a>[Centro de Administração windows](#tab/windowsadmincenter)

1. No **painel de provisionamento do dispositivo Azure IoT Edge,** selecione **a Chave Simétrica (DPS)** do método de provisionamento.

1. No [portal Azure,](https://ms.portal.azure.com/)navegue para a sua instância DPS.

1. No **separador Visão Geral,** copie o valor **ID Scope.** Cole-o no campo de identificação de âmbito no Windows Admin Center.

1. No **separador Gestão de inscrições** no portal Azure, selecione a inscrição que criou. Copie o valor **da Chave Primária** nos detalhes da inscrição. Cole-o no campo de chaves simétrica no Windows Admin Center.

1. Forneça o ID de registo do seu dispositivo no campo de identificação de registo no Windows Admin Center.

1. Escolha **Provisioning com o método selecionado.**

   ![Escolha o provisionamento com o método selecionado após o preenchimento dos campos necessários para o provisionamento de chaves simétricas](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Uma vez que o provisionamento esteja concluído, **selecione Terminar**. Serão levados de volta para o painel principal. Agora, deve ver um novo dispositivo listado, cujo tipo é `IoT Edge Devices` . Pode selecionar o dispositivo IoT Edge para se ligar ao mesmo. Uma vez na sua página **de visão geral,** pode ver a Lista de **Módulos IoT Edge** e o Estado da **Borda IoT** do seu dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copie o comando seguinte para um editor de texto. Substitua o texto do espaço reservado pelas suas informações conforme detalhado.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. No [portal Azure,](https://ms.portal.azure.com/)navegue para a sua instância DPS.

1. No **separador Visão Geral,** copie o valor **ID Scope.** Cole-o sobre o texto reservado apropriado no comando.

1. No **separador Gestão de inscrições** no portal Azure, selecione a inscrição que criou. Copie o valor **da Chave Primária** nos detalhes da inscrição. Cole-o sobre o texto reservado apropriado no comando.

1. Forneça o ID de registo do dispositivo para substituir o texto reservado apropriado no comando.

1. Executar o comando numa sessão de PowerShell elevada no dispositivo alvo.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Opção 3: Provisionamento via DPS utilizando certificados X.509

Esta secção abrange o fornecimento do seu dispositivo automaticamente utilizando certificados DPS e X.509.

# <a name="windows-admin-center"></a>[Centro de Administração windows](#tab/windowsadmincenter)

1. No **painel de provisionamento do dispositivo Azure IoT Edge,** selecione **O Certificado X.509 (DPS)** do método de provisionamento.

1. No [portal Azure,](https://ms.portal.azure.com/)navegue para a sua instância DPS.

1. No **separador Visão Geral,** copie o valor **ID Scope.** Cole-o no campo de identificação de âmbito no Windows Admin Center.

1. Forneça o ID de registo do seu dispositivo no campo de identificação de registo no Windows Admin Center.

1. Faça upload do seu certificado e ficheiros de chaves privadas.

1. Escolha **Provisioning com o método selecionado.**

   ![Escolha o provisionamento com o método selecionado após o preenchimento dos campos necessários para o provisionamento do certificado X.509](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Uma vez que o provisionamento esteja concluído, **selecione Terminar**. Serão levados de volta para o painel principal. Agora, deve ver um novo dispositivo listado, cujo tipo é `IoT Edge Devices` . Pode selecionar o dispositivo IoT Edge para se ligar ao mesmo. Uma vez na sua página **de visão geral,** pode ver a Lista de **Módulos IoT Edge** e o Estado da **Borda IoT** do seu dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copie o comando seguinte para um editor de texto. Substitua o texto do espaço reservado pelas suas informações conforme detalhado.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. No [portal Azure,](https://ms.portal.azure.com/)navegue para a sua instância DPS.

1. No **separador Visão Geral,** copie o valor **ID Scope.** Cole-o sobre o texto reservado apropriado no comando.

1. Forneça o ID de registo do dispositivo para substituir o texto reservado apropriado no comando.

1. Substitua o texto de espaço reservado apropriado pelo caminho de origem absoluta do seu ficheiro de certificado.

1. Substitua o texto do espaço reservado apropriado pelo caminho de origem absoluta para o seu ficheiro de chave privada.

1. Executar o comando numa sessão de PowerShell elevada no dispositivo alvo.

---

## <a name="verify-successful-configuration"></a>Verifique a configuração bem sucedida

Verifique se o IoT Edge para Linux no Windows foi instalado com sucesso e configurado no seu dispositivo IoT Edge.

1. Selecione o seu dispositivo IoT Edge a partir da lista de dispositivos conectados no Windows Admin Center para ligar ao mesmo.
1. A página geral do dispositivo apresenta algumas informações sobre o dispositivo:

    1. A secção **IoT Edge Module List** mostra módulos de execução no dispositivo. Quando o serviço IoT Edge começar pela primeira vez, só deverá ver o módulo **edgeAgent** a funcionar. O módulo EdgeAgent funciona por predefinição e ajuda a instalar e iniciar quaisquer módulos adicionais que implemente no seu dispositivo.
    1. A secção **IoT Edge Status** mostra o estado do serviço e deve estar a reportar **ativa (em funcionamento)**.

1. Se precisar de resolver problemas no serviço IoT Edge, utilize a ferramenta **Comando Shell** na página do dispositivo para se aproximar (concha segura) na máquina virtual e executar os comandos Linux.

    1. Se precisar de resolver problemas relacionados com o serviço, obtenha os registos do serviço.

       ```bash
       journalctl -u iotedge
       ```

    2. Utilize a `check` ferramenta para verificar a configuração e o estado de ligação do dispositivo.

       ```bash
       sudo iotedge check
       ```

## <a name="next-steps"></a>Passos seguintes

Continue a [implementar módulos IoT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos no seu dispositivo.
