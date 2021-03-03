---
title: Atualização do dispositivo para tutorial Azure IoT Hub utilizando o agente de pacote ubuntu Server 18.04 x64 | Microsoft Docs
description: Inicie com a Atualização do Dispositivo para O Hub IoT do Azure utilizando o agente pacote Ubuntu Server 18.04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665181"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Atualização do dispositivo para tutorial Azure IoT Hub utilizando o agente de pacote ubuntu Server 18.04 x64

A Atualização do Dispositivo para o IoT Hub suporta duas formas de atualizações – baseadas em imagem e baseadas em pacotes.

As atualizações baseadas em pacotes são atualizações direcionadas que alteram apenas um componente ou aplicação específico no dispositivo. Isto leva a um menor consumo de largura de banda e ajuda a reduzir o tempo para descarregar e instalar a atualização. As atualizações de pacotes normalmente permitem menos tempo de inatividade dos dispositivos ao aplicar uma atualização e evitar a sobrecarga de criação de imagens.

Este tutorial acompanha-o através dos passos para completar uma atualização de pacotes de ponta a ponta através da Atualização do Dispositivo para ioT Hub. Usaremos um agente de pacote de amostras para o Ubuntu Server 18.04 x64 para este tutorial. Mesmo que planeie utilizar uma configuração diferente da plataforma OS, este tutorial ainda é útil para aprender sobre as ferramentas e conceitos em Device Update para IoT Hub. Complete esta introdução a um processo de atualização de ponta a ponta e, em seguida, escolha a sua forma preferida de atualização e plataforma de SO para mergulhar nos detalhes. Pode utilizar a Atualização do Dispositivo para o IoT Hub para atualizar um dispositivo Azure IoT ou Azure IoT Edge utilizando este tutorial. 

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Configurar o repositório de pacote de atualização do dispositivo
> * Descarregue e instale o agente de atualização do dispositivo e as suas dependências
> * Adicione uma etiqueta ao seu dispositivo IoT
> * Importar uma atualização
> * Criar um grupo de dispositivos
> * Implementar uma atualização de pacote
> * Monitorize a implementação da atualização

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* Acesso a um hub IoT. Recomenda-se que utilize um nível S1 (Standard) ou superior.
* Um dispositivo Azure IoT ou Azure IoT Edge com o Ubuntu Server 18.04 x64, ligado ao IoT Hub.
   * Se estiver a utilizar um dispositivo Azure IoT Edge, certifique-se de que está em v1.2.0 do tempo de execução edge ou superior 
* Se não estiver a utilizar um dispositivo Azure IoT Edge, [instale o pacote mais recente `aziot-identity-service` (pré-visualização) no seu dispositivo IoT](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [Conta e instância de Atualização de Dispositivos ligadas ao mesmo Hub IoT acima.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Configurar o repositório de pacote de atualização do dispositivo

1. Instale a configuração do repositório que corresponda ao sistema operativo do dispositivo. Para este tutorial, este será o Ubuntu Server 18.04. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Copie a lista gerada para o diretório sources.list.d.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Instale a chave pública Microsoft GPG.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Instalar pacotes de agentes de .deb de atualização do dispositivo

1. Atualizar listas de pacotes no seu dispositivo

   ```shell
      sudo apt-get update
   ```

2. Instale o pacote de agente de dispositivos e as suas dependências

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

A atualização do dispositivo para pacotes de software Azure IoT Hub estão sujeitas aos seguintes termos de licença:
   * [Atualização do dispositivo para licença IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licença de cliente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Leia os termos da licença antes de utilizar uma embalagem. A sua instalação e utilização de um pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize esse pacote.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Configure o agente de atualização do dispositivo utilizando o serviço de identidade Azure IoT (Pré-visualização)

Uma vez instaladas as embalagens necessárias, é necessário doar ao dispositivo a sua identidade na nuvem e informações de autenticação.

1. Abra o ficheiro de configuração

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. Encontre a secção de configuração de provisionamento do ficheiro. Descodução da secção "Provisionamento manual com fio de ligação". Atualize o valor do connection_string com a cadeia de ligação para o seu dispositivo IoT (Edge). Certifique-se de que todas as outras secções de provisionamento são comentadas.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Guarde e feche o ficheiro usando Ctrl+X, Y

4. Aplique a configuração. 

   Se estiver a utilizar um dispositivo IoT Edge, utilize o seguinte comando. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Se estiver a utilizar um dispositivo IoT, com a `aziot-identity-service` embalagem instalada, utilize o seguinte comando. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. Opcionalmente, pode verificar se os serviços estão a decorrer

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    A saída deve ler-se:

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
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

1. Faça o download do [ficheiro manifesto apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) seguinte e [importe ficheiro manifesto.](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json) Este manifesto apt irá instalar a versão mais recente disponível do `libcurl4-doc package` seu dispositivo IoT. 

   Em alternativa, pode descarregar este [ficheiro manifesto apto](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) e importar ficheiro [manifesto.](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json) Isto irá instalar a versão específica v7.58.0 do `libcurl4-doc package` dispositivo IoT. 

2. No portal Azure, selecione a opção Atualizações de Dispositivos sob Gestão Automática de Dispositivos a partir da barra de navegação à esquerda no seu Hub IoT.

3. Selecione o separador Atualizações.

4. Selecione "+ Import New Update".

5. Selecione o ícone de pasta ou caixa de texto em "Selecione um Ficheiro Manifesto de Importação". Verá um diálogo de selecionador de ficheiros. Selecione o Manifesto de Importação que descarregou anteriormente. Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecione um ou mais ficheiros de atualização". Verá um diálogo de selecionador de ficheiros. Selecione o ficheiro de atualização manifesto apt que descarregou anteriormente.
   
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

Já concluiu uma atualização de pacotes de ponta a ponta com sucesso utilizando a Atualização do Dispositivo para IoT Hub num dispositivo Ubuntu Server 18.04 x64. 

## <a name="bonus-steps"></a>Passos de bónus

1. Faça o download do [ficheiro manifesto apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) seguinte e [importe ficheiro manifesto.](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json) Este manifesto apto removerá o instalado `libcurl4-doc package` do seu dispositivo IoT. 

2. Repita as secções "Atualização de Importação" e "Implementar atualização"

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, limpe a conta de atualização do dispositivo, caso, IoT Hub e dispositivo IoT. Pode fazê-lo, indo a cada recurso individual e selecionando "Delete". Tenha em atenção que é necessário limpar uma instância de atualização do dispositivo antes de limpar a conta de atualização do dispositivo. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atualização de imagem no tutorial de Framboesa Pi 3 B+](device-update-raspberry-pi.md)

