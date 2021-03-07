---
title: Atualização do dispositivo para tutorial Azure IoT Hub utilizando o Agente de Referência do Simulador Ubuntu (18,04 x64) | Microsoft Docs
description: Inicie com a Atualização do Dispositivo para O Hub IoT Azure utilizando o Agente de Referência do Simulador Ubuntu (18,04 x64).
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 4740bf02c33314dd7c887356f2ef1ed12bea44cf
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443816"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Atualização do dispositivo para tutorial Azure IoT Hub utilizando o Agente de Referência do Simulador Ubuntu (18,04 x64)

A Atualização do Dispositivo para o IoT Hub suporta duas formas de atualizações – baseadas em imagem e baseadas em pacotes.

As atualizações de imagem proporcionam um maior nível de confiança no estado final do dispositivo. É tipicamente mais fácil replicar os resultados de uma atualização de imagem entre um ambiente de pré-produção e um ambiente de produção, uma vez que não coloca os mesmos desafios que os pacotes e as suas dependências. Devido à sua natureza atómica, também se pode adotar facilmente um modelo de failover A/B.

Este tutorial acompanha-o através dos passos para completar uma atualização baseada em imagem de ponta a ponta utilizando a Atualização do Dispositivo para o IoT Hub. 

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Descarregar e instalar imagem
> * Adicione uma etiqueta ao seu dispositivo IoT
> * Importar uma atualização
> * Criar um grupo de dispositivos
> * Implementar uma atualização de imagem
> * Monitorize a implementação da atualização

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* Acesso a um hub IoT. Recomenda-se que utilize um nível S1 (Standard) ou superior.

### <a name="download-and-install"></a>Transferir e instalar

* Cmdlets Az (Azure CLI) para PowerShell:
  * Open PowerShell > Instalar O Azure CLI ("Y" para instruções para instalar a partir de fonte "não confiável")

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Ativar o WSL no seu dispositivo Windows (Subsistema Windows para Linux)

1. Abrir PowerShell como administrador na sua máquina e executar o seguinte comando (pode ser solicitado que reinicie após cada passo; reinicie quando lhe perguntarem):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(Pode *ser solicitado que reinicie após este passo)*

2. Vá à Microsoft Store na web e instale [Ubuntu 18.04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Inicie "Ubuntu 18.04 LTS" e instale.

4. Quando instalado, será solicitado que desemote o nome de raiz (nome de utilizador) e a palavra-passe. Certifique-se de usar uma senha de nome de raiz memorável.

5. No PowerShell, executar o seguinte comando para definir Ubuntu como a distribuição linux padrão:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Enuda todas as distribuições linux, certifique-se de que Ubuntu é o padrão.

```powershell
PS> wsl --list
```

7. Você deve ver: **Ubuntu-18.04 (Padrão)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Baixar Dispositivo Update Ubuntu (18.04 x64) Agente de referência do simulador

A imagem de atualização Ubuntu pode ser descarregada a partir da secção *Ativos* a partir de notas de lançamento [aqui](https://github.com/Azure/iot-hub-device-update/releases).

Há duas versões do agente. Se estiver a exercer um cenário baseado na imagem, use a AducIotAgentSim-microsoft-swupdate e se estiver a exercer um cenário baseado em pacotes, utilize o AducIotAgentSim-microsoft-apt.

## <a name="install-device-update-agent-simulator"></a>Instale o simulador do agente de atualização do dispositivo

1. Inicie a Ubuntu WSL e introduza o seguinte comando (note que espaço extra e ponto no final).

  ```shell
  explorer.exe .
  ```

2. Copie AducIotAgentSim-microsoft-swupdate (ou AducIotAgentSim-microsoft-apt) da sua pasta local onde foi descarregado em /mnt para a sua pasta de casa em WSL.

3. Execute o seguinte comando para tornar as binários executáveis.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  ou

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
A atualização do dispositivo para o software Azure IoT Hub está sujeita aos seguintes termos de licença:
   * [Atualização do dispositivo para licença IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licença de cliente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Leia os termos da licença antes de utilizar o agente. A sua instalação e utilização constituem a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize a atualização do Dispositivo para o agente IoT Hub.

## <a name="add-device-to-azure-iot-hub"></a>Adicione o dispositivo ao Azure IoT Hub

Uma vez que o Agente de Atualização do Dispositivo esteja em funcionamento num dispositivo IoT, o dispositivo precisa de ser adicionado ao Hub Azure IoT.  A partir de dentro do Azure IoT Hub, será gerada uma cadeia de ligação para um determinado dispositivo.

1. A partir do portal Azure, lance o Hub IoT de Atualização de Dispositivos.
2. Criar um novo dispositivo.
3. No lado esquerdo da página, navegue para 'Explorers' > 'Dispositivos IoT' > Selecione "Novo".
4. Fornecer um nome para o dispositivo em 'Device ID'--Certifique-se de que "Teclas de autogerado" é selecionada.
5. Selecione 'Guardar'.
6. Agora, será devolvido à página 'Dispositivos' e o dispositivo que criou deve estar na lista. Selecione o dispositivo.
7. Na vista do dispositivo, selecione o ícone 'Copy' ao lado de 'Cadeia de Ligação Primária'.
8. Cole os caracteres copiados em algum lugar para posterior utilização nos degraus abaixo. **Esta corda copiada é a cadeia de ligação do seu dispositivo**.

## <a name="add-connection-string-to-simulator"></a>Adicione a cadeia de ligação ao simulador

Inicie o agente de atualização do dispositivo nos seus novos dispositivos de software.

1. Começa ubuntu.
2. Executar o Agente de Atualização do Dispositivo e especificar a cadeia de ligação do dispositivo da secção anterior embrulhada com apóstrofos:

`<device connection string>`Substitua-a pela sua cadeia de ligação
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

ou

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Percorra e procure a corda indicando que o dispositivo está em estado "Ocioso". Um estado "Idle" significa que o dispositivo está pronto para comandos de serviço:

```markdown
Agent running. [main]
```

## <a name="add-a-tag-to-your-device"></a>Adicione uma etiqueta ao seu dispositivo

1. Entre no [portal Azure](https://portal.azure.com) e navegue até ao Hub IoT.

2. De 'Dispositivos IoT' ou 'IoT Edge' no painel de navegação esquerdo encontre o seu dispositivo IoT e navegue para o Dispositivo Twin.

3. No Dispositivo Twin, elimine qualquer valor de marcação de atualização do dispositivo existente, definindo-os para nulos.

4. Adicione um novo valor de etiqueta de atualização do dispositivo, como mostrado abaixo.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Atualização de importação

1. Crie um Manifesto de Importação seguindo estas [instruções.](import-update.md)
2. Selecione a opção Atualizações de Dispositivos sob Gestão Automática de Dispositivos a partir da barra de navegação à esquerda.

3. Selecione o separador Atualizações.

4. Selecione "+ Import New Update".

5. Selecione o ícone de pasta ou caixa de texto em "Selecione um Ficheiro Manifesto de Importação". Verá um diálogo de selecionador de ficheiros. Selecione o Manifesto de Importação que criou acima.  Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecione um ou mais ficheiros de atualização". Verá um diálogo de selecionador de ficheiros. Selecione a imagem de atualização Ubuntu que descarregou anteriormente. 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Screenshot mostrando a seleção de ficheiros de atualização." lightbox="media/import-update/select-update-files.png":::

6. Selecione o ícone de pasta ou caixa de texto em "Selecione um recipiente de armazenamento". Em seguida, selecione a conta de armazenamento apropriada.

7. Se já criou um contentor, pode reutilizá-lo. (Caso contrário, selecione "+ Container" para criar um novo recipiente de armazenamento para atualizações.).  Selecione o recipiente que pretende utilizar e clique em "Selecione".
  
  :::image type="content" source="media/import-update/container.png" alt-text="Screenshot mostrando a seleção do recipiente." lightbox="media/import-update/container.png":::

8. Selecione "Submeter" para iniciar o processo de importação.

9. O processo de importação começa e o ecrã muda para a secção "Import History". Selecione "Refresh" para ver o progresso até que o processo de importação esteja concluído. Dependendo do tamanho da atualização, isto pode completar em poucos minutos, mas pode demorar mais tempo.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Screenshot mostrando a sequência de importação de atualização." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando a coluna Status indicar que a importação foi bem sucedida, selecione o cabeçalho "Pronto a Implantar". Devia ver a sua atualização importada na lista agora.

[Saiba mais](import-update.md) sobre a importação de atualizações.

## <a name="create-update-group"></a>Criar grupo de atualização

1. Aceda ao Hub IoT que já ligou à sua instância de Atualização de Dispositivos.

2. Selecione a opção Atualizações de Dispositivos sob Gestão Automática de Dispositivos a partir da barra de navegação à esquerda.

3. Selecione o separador Grupos no topo da página. 

4. Selecione o botão Adicionar para criar um novo grupo.

5. Selecione a etiqueta IoT Hub que criou no passo anterior da lista. Selecione Criar grupo de atualização.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot mostrando a seleção da etiqueta." lightbox="media/create-update-group/select-tag.PNG":::

[Saiba mais](create-update-group.md) sobre a adição de tags e criar grupos de atualização


## <a name="deploy-update"></a>Implementar atualização

1. Uma vez criado o grupo, deverá ver uma nova atualização disponível para o seu grupo de dispositivos, com uma ligação à atualização em 'Atualizações Pendentes'. Pode precisar de refrescar uma vez. 

2. Clique na atualização disponível.

3. Confirme que o grupo correto está selecionado como grupo alvo. Agendar a sua implementação e, em seguida, selecione Implementar a atualização.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecione atualização" lightbox="media/deploy-update/select-update.png":::

4. Veja o gráfico de conformidade. Devia ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em curso" lightbox="media/deploy-update/update-in-progress.png":::

5. Depois de o seu dispositivo ter sido atualizado com sucesso, deverá ver a atualização do seu gráfico de conformidade e detalhes de implementação para refletir o mesmo. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização conseguiu" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorize uma implementação de atualização

1. Selecione o separador Implementações no topo da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Separador de implementações" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecione a implementação criada para ver os detalhes da implementação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes da implementação" lightbox="media/deploy-update/deployment-details.png":::

3. Selecione Refresh para ver os mais recentes detalhes do estado. Continue este processo até que o estado mude para Sucesso.

Já concluiu uma atualização de imagem de ponta a ponta com sucesso utilizando a Atualização do Dispositivo para IoT Hub utilizando o Agente de Referência do Simulador Ubuntu (18.04 x64).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, limpe a conta de atualização do dispositivo, caso, IoT Hub e dispositivo IoT. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Resolução de problemas](troubleshoot-device-update.md)

