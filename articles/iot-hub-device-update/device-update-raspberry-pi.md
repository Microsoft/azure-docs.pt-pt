---
title: Atualização do dispositivo para tutorial Azure IoT Hub utilizando a imagem yocto de referência de Framboesa Pi 3 B+ | Microsoft Docs
description: Começa com a Atualização do Dispositivo para O Hub IoT Azure utilizando a Raspberry Pi 3 B+ Reference Yocto Image.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: b6a9fa3ac85460a46653c171198a2dfea8580f3a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644495"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Atualização do dispositivo para tutorial Azure IoT Hub usando a imagem de referência Raspberry Pi 3 B+

A Atualização do Dispositivo para o IoT Hub suporta duas formas de atualizações – baseadas em imagem e baseadas em pacotes.

As atualizações de imagem proporcionam um maior nível de confiança no estado final do dispositivo. É tipicamente mais fácil replicar os resultados de uma atualização de imagem entre um ambiente de pré-produção e um ambiente de produção, uma vez que não coloca os mesmos desafios que os pacotes e as suas dependências. Devido à sua natureza atómica, também se pode adotar facilmente um modelo de failover A/B.

Este tutorial acompanha-o através dos passos para completar uma atualização baseada em imagem de ponta a ponta utilizando a Atualização do Dispositivo para o IoT Hub. 

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Baixar imagem
> * Adicione uma etiqueta ao seu dispositivo IoT
> * Importar uma atualização
> * Criar um grupo de dispositivos
> * Implementar uma atualização de imagem
> * Monitorize a implementação da atualização

## <a name="prerequisites"></a>Pré-requisitos
* Se ainda não o fez, crie uma [conta e exemplo de Atualização de Dispositivos](create-device-update-account.md), incluindo configurar um Hub IoT.

## <a name="download-image"></a>Baixar imagem

Existem três imagens disponíveis como parte dos "Ativos" numa determinada [versão do GitHub de Atualização de Dispositivos](https://github.com/Azure/iot-hub-device-update/releases). A imagem base (imagem adu-base) e uma imagem de atualização (adu-update-image) são fornecidas para que possa experimentar lançamentos para diferentes versões sem precisar de piscar o cartão SD no dispositivo. Para tal, terá de enviar as imagens de atualização para a Atualização do Dispositivo para o Serviço IoT Hub, como parte da importação.

## <a name="flash-sd-card-with-image"></a>Cartão Flash SD com imagem

Utilizando a sua ferramenta de piscar de olhos de SO favorito, instale a imagem base de Atualização do Dispositivo (imagem adu-base) no Cartão SD que será utilizado no dispositivo Raspberry Pi 3 B+.

### <a name="using-bmaptool-to-flash-sd-card"></a>Usando bmaptool para flash SD card

1. Se ainda não o fez, instale o `bmaptool` utilitário.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Localize o caminho para o cartão SD em `/dev` . O caminho deve parecer algo como `/dev/sd*` `/dev/mmcblk*` ou. Pode utilizar o `dmesg` utilitário para ajudar a localizar o caminho correto.

3. Terá de desmontar todas as divisórias montadas antes de piscar.

   ```shell
   sudo umount /dev/<device>
   ```

4. Certifique-se de que tem permissões de escrita para o dispositivo.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. Opcional. Para piscar mais rapidamente, descarregue o ficheiro bimap juntamente com o ficheiro de imagem e coloque-o no mesmo diretório.

6. Flash o cartão SD.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
A atualização do dispositivo para o software Azure IoT Hub está sujeita aos seguintes termos de licença:
   * [Atualização do dispositivo para licença IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licença de cliente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Leia os termos da licença antes de utilizar o agente. A sua instalação e utilização constituem a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize a atualização do Dispositivo para o agente IoT Hub.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>Crie dispositivo no IoT Hub e obtenha a cadeia de ligação

Agora, o dispositivo precisa de ser adicionado ao Azure IoT Hub.  A partir de dentro do Azure IoT Hub, será gerada uma cadeia de ligação para o dispositivo.

1. A partir do portal Azure, lance o Hub IoT de Atualização de Dispositivos.
2. Criar um novo dispositivo.
3. No lado esquerdo da página, navegue para 'Explorers' > 'Dispositivos IoT' > Selecione "Novo".
4. Fornecer um nome para o dispositivo em 'Device ID'--Certifique-se de que "Teclas de autogerado" é selecionada.
5. Selecione 'Guardar'.
6. Agora será devolvido à página 'Dispositivos' e o dispositivo que criou deverá estar na lista. Selecione o dispositivo.
7. Na vista do dispositivo, selecione o ícone 'Copy' ao lado de 'Cadeia de Ligação Primária'.
8. Cole os caracteres copiados em algum lugar para posterior utilização nos degraus abaixo.
   **Esta corda copiada é a cadeia de ligação do seu dispositivo**.

## <a name="provision-connection-string-on-sd-card"></a>Cadeia de conexão de provisão no cartão SD

1. Certifique-se de que o Raspberry Pi3 está ligado à rede.
2. Em PowerShell, use o comando abaixo para entrar no dispositivo
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Introduza o login como 'raiz', e a palavra-passe deve ser deixada como vazia.
5. Depois de ter conseguido entrar no dispositivo, corra os comandos abaixo
 
`<device connection string>`Substitua-a pela sua cadeia de ligação
 ```markdown
    echo "connection_string=<device connection string>" > adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> adu-conf.txt
    echo "aduc_model=RefDevice" >> adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Ligue o dispositivo no Hub IoT da Atualização do Dispositivo

1. No lado esquerdo da página, selecione 'Dispositivos IoT' em 'Exploradores'.
2. Selecione a ligação com o nome do seu dispositivo.
3. No topo da página, selecione 'Device Twin'.
4. Na secção 'reportada' das propriedades gémeas do dispositivo, procure a versão do kernel Linux.
Para um novo dispositivo, que não tenha recebido uma atualização da Atualização do Dispositivo, o valor [DeviceManagement:DeviceInformation:1.swVersion](device-update-plug-and-play.md) representará a versão firmware em execução no dispositivo.  Uma vez aplicada uma atualização a um dispositivo, a Atualização do Dispositivo utilizará o valor da propriedade [AzureDeviceUpdateCore:ClientMetadata:4.installedUpdateId](device-update-plug-and-play.md) para representar a versão firmware em execução no dispositivo.
5. Os ficheiros de imagem base e de atualização têm um número de versão no nome de ficheiro.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Utilize este número de versão no passo abaixo da Atualização de Importação.

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
5. Selecione o ícone de pasta ou caixa de texto em "Selecione um Ficheiro Manifesto de Importação". Verá um diálogo de selecionador de ficheiros. Selecione o Manifesto de Importação que criou acima.  Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecione um ou mais ficheiros de atualização". Verá um diálogo de selecionador de ficheiros. Selecione o ficheiro de atualização que deseja implementar nos seus dispositivos IoT.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Screenshot mostrando a seleção de ficheiros de atualização." lightbox="media/import-update/select-update-files.png":::

5. Selecione o ícone de pasta ou caixa de texto em "Selecione um recipiente de armazenamento". Em seguida, selecione a conta de armazenamento apropriada.

6. Se já criou um contentor, pode reutilizá-lo. (Caso contrário, selecione "+ Container" para criar um novo recipiente de armazenamento para atualizações.).  Selecione o recipiente que pretende utilizar e clique em "Selecione".
  
  :::image type="content" source="media/import-update/container.png" alt-text="Screenshot mostrando a seleção do recipiente." lightbox="media/import-update/container.png":::

7. Selecione "Submeter" para iniciar o processo de importação.

8. O processo de importação começa e o ecrã muda para a secção "Import History". Selecione "Refresh" para ver o progresso até que o processo de importação esteja concluído. Dependendo do tamanho da atualização, isto pode completar em poucos minutos, mas pode demorar mais tempo.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Screenshot mostrando a sequência de importação de atualização." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Quando a coluna Status indicar que a importação foi bem sucedida, selecione o cabeçalho "Pronto a Implantar". Devia ver a sua atualização importada na lista agora.

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

Já concluiu uma atualização de imagem de ponta a ponta com sucesso utilizando a Atualização do Dispositivo para IoT Hub num dispositivo Raspberry Pi 3 B+. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, limpe a conta de atualização do dispositivo, caso, IoT Hub e dispositivo IoT. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Agente de referência do simulador](device-update-simulator.md)
