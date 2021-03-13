---
title: Atualização do dispositivo para tutorial Azure IoT Hub utilizando o agente de pacote ubuntu Server 18.04 x64 | Microsoft Docs
description: Inicie com a Atualização do Dispositivo para O Hub IoT do Azure utilizando o agente pacote Ubuntu Server 18.04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: f7e12567269304b33a98ff1eb9727cfdf0afbdc4
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418645"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Atualização do dispositivo para tutorial Azure IoT Hub usando o agente de pacotes no Ubuntu Server 18.04 x64

A Atualização do Dispositivo para o IoT Hub suporta duas formas de atualizações – baseadas em imagem e baseadas em pacotes.

As atualizações baseadas em pacotes são atualizações direcionadas que alteram apenas um componente ou aplicação específico no dispositivo. Isto leva a um menor consumo de largura de banda e ajuda a reduzir o tempo para descarregar e instalar a atualização. As atualizações de pacotes normalmente permitem menos tempo de inatividade dos dispositivos ao aplicar uma atualização e evitar a sobrecarga de criação de imagens.

Este tutorial acompanha-o através dos passos para completar uma atualização de pacotes de ponta a ponta através da Atualização do Dispositivo para ioT Hub. Para este tutorial utilizamos um Ubuntu Server 18.04 x64 que executa a Azure IoT Edge e o agente de pacotes de Atualização de Dispositivos. O tutorial demonstra a atualização de um pacote de amostras, mas usando passos semelhantes, pode atualizar outros pacotes, como o Azure IoT Edge ou o motor de contentores que utiliza.

As ferramentas e conceitos deste tutorial ainda se aplicam mesmo que planeie usar uma configuração diferente da plataforma OS. Complete esta introdução a um processo de atualização de ponta a ponta e, em seguida, escolha a sua forma preferida de atualização e plataforma de SO para mergulhar nos detalhes.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Descarregue e instale o agente de Atualização de Dispositivos e as suas dependências
> * Adicione uma etiqueta ao seu dispositivo
> * Importar uma atualização
> * Criar um grupo de dispositivos
> * Implementar uma atualização de pacote
> * Monitorize a implementação da atualização

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Acesso a um hub IoT. Recomenda-se que utilize um nível S1 (Standard) ou superior.
* Uma instância e conta de atualização de dispositivos ligada ao seu Hub IoT.
  * Siga o guia para [criar e ligar uma conta de atualização do dispositivo](create-device-update-account.md) se não o tiver feito anteriormente.
* A [cadeia de ligação para um dispositivo IoT Edge](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Preparar um dispositivo
### <a name="using-the-automated-deploy-to-azure-button"></a>Utilizando o botão De implantação automatizada para Azure

Por conveniência, este tutorial utiliza um modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) baseado em nuvem para [ajudá-lo](../virtual-machines/linux/using-cloud-init.md)a configurar rapidamente uma máquina virtual Ubuntu 18.04 LTS. Instala tanto o tempo de funcionamento do Azure IoT Edge como o agente de pacotes de atualização do dispositivo e, em seguida, configura automaticamente o dispositivo com informações de provisionamento utilizando a cadeia de ligação do dispositivo para um dispositivo IoT Edge (pré-requisito) que fornece. Isto evita a necessidade de iniciar uma sessão de SSH para completar a configuração.

1. Para começar, clique no botão abaixo:

   [![Botão Implementar no Azure iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2F1.2.0-rc4%2FedgeDeploy.json)

1. Na janela recém-lançada, preencha os campos de formulários disponíveis:

    > [!div class="mx-imgBorder"]
    > [![Captura de ecrã a mostrar o modelo iotedge-vm-deploy](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Subscrição**: A subscrição ativa do Azure para implantar a máquina virtual.

    **Grupo de recursos**: Um Grupo de Recursos existente ou recém-criado para conter a máquina virtual e os seus recursos associados.

    **Prefixo da etiqueta DO DNS**: Um valor necessário à sua escolha que é usado para pré-fixar o nome de anfitrião da máquina virtual.

    **Nome de utilizador Admin**: Um nome de utilizador, que será fornecido privilégios de raiz na implementação.

    **Cadeia de ligação do dispositivo**: Uma [cadeia de ligação](../iot-edge/how-to-register-device.md) do dispositivo para um dispositivo que foi criado dentro do seu [hub IoT](../iot-hub/about-iot-hub.md)pretendido .

    **Tamanho VM**: O [tamanho](../cloud-services/cloud-services-sizes-specs.md) da máquina virtual a ser implantada

    **Versão Ubuntu OS**: A versão do Ubuntu OS a ser instalada na máquina virtual base. Deixe o valor padrão inalterado, uma vez que será definido para Ubuntu 18.04-LTS já.

    **Localização**: A [região geográfica](https://azure.microsoft.com/global-infrastructure/locations/) para implantar a máquina virtual, este valor desresídeo à localização do Grupo de Recursos selecionado.

    **Tipo de autenticação**: Escolha **sshPublicKey** ou **palavra-passe** dependendo da sua preferência.

    **Palavra-passe ou chave de administração**: O valor da Chave Pública SSH ou o valor da palavra-passe dependendo da escolha do Tipo de Autenticação.

    Quando todos os campos tiverem sido preenchidos, selecione a caixa de verificação na parte inferior da página para aceitar os termos e selecione **Comprar** para iniciar a implementação.

1. Verifique se a implementação foi concluída com êxito. Deixe alguns minutos após a implementação concluída para que a pós-instalação e configuração termine de instalar ioT Edge e o agente de atualização do Pacote de Dispositivos.

   Um recurso da máquina virtual devia ter sido implementado no grupo de recursos selecionados.  Tome nota do nome da máquina, este deve estar no formato `vm-0000000000000` . Além disso, tome nota do **Nome DNS** associado, que deve estar no formato `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    O **Nome DNS** pode ser obtido na secção **Descrição geral** da máquina virtual recentemente implementada no portal do Azure.

    > [!div class="mx-imgBorder"]
    > [![Screenshot mostrando o nome dns do iotedge vm](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Se pretender entrar neste VM após a configuração, utilize o **Nome DNS** associado com o comando: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>(Opcional) Preparar manualmente um dispositivo
Os seguintes passos manuais para instalar e configurar o dispositivo são equivalentes aos que foram automatizados por este [script de inicio de nuvem](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt). Podem ser usados para preparar um dispositivo físico.

1. Siga as instruções para [instalar o tempo de funcionamento do Aresta Azure IoT](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > O agente de pacotes de atualização de dispositivos não depende do IoT Edge. Mas, conta com o daemon IoT Identity Service que está instalado com IoT Edge (1.2.0 ou superior) para obter uma identidade e ligar-se ao IoT Hub.
   >
   > Apesar de não estar coberto por este tutorial, o [daemon IoT Identity Service pode ser instalado autónomo em dispositivos IoT baseados em Linux](https://azure.github.io/iot-identity-service/packaging.html). A sequência de instalação importa. O agente de pacotes de atualização do dispositivo deve ser instalado _após_ o Serviço de Identidade IoT. Caso contrário, o agente de embalagem não será registado como um componente autorizado para estabelecer uma ligação ao IoT Hub.

1. Em seguida, instale o agente de atualização do dispositivo .deb pacotes.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

A atualização do dispositivo para pacotes de software Azure IoT Hub estão sujeitas aos seguintes termos de licença:
  * [Atualização do dispositivo para licença IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Licença de cliente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/LICENSE)

Leia os termos da licença antes de utilizar uma embalagem. A sua instalação e utilização de um pacote constitui a sua aceitação destes termos. Se não concordar com os termos da licença, não utilize esse pacote.

## <a name="add-a-tag-to-your-device"></a>Adicione uma etiqueta ao seu dispositivo

1. Entre no [portal Azure](https://portal.azure.com) e navegue até ao Hub IoT.

2. A partir de 'IoT Edge' no painel de navegação esquerdo encontre o seu dispositivo IoT Edge e navegue até ao Dispositivo Twin.

3. No Dispositivo Twin, elimine qualquer valor de marcação de atualização do dispositivo existente, definindo-os para nulos.

4. Adicione um novo valor de etiqueta de atualização do dispositivo, como mostrado abaixo.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Atualização de importação

1. Aceda às [versões de Atualização de Dispositivos](https://github.com/Azure/iot-hub-device-update/releases) no Github e clique na queda de "Ativos".

3. Faça o download `apt-update-import-samples.zip` clicando nele.

5. Extrair o conteúdo da pasta para descobrir várias amostras de atualização e os respetivos manifestos de importação. 

2. No portal Azure, selecione a opção Atualizações de Dispositivos sob Gestão Automática de Dispositivos a partir da barra de navegação à esquerda no seu Hub IoT.

3. Selecione o separador Atualizações.

4. Selecione "+ Import New Update".

5. Selecione o ícone de pasta ou caixa de texto em "Selecione um Ficheiro Manifesto de Importação". Verá um diálogo de selecionador de ficheiros. Selecione o `sample-package-update-1.0.1-importManifest.json` manifesto de importação da pasta que descarregou anteriormente. Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecione um ou mais ficheiros de atualização". Verá um diálogo de selecionador de ficheiros. Selecione o `sample-1.0.1-libcurl4-doc-apt-manifest.json` ficheiro de atualização manifesto apt da pasta que descarregou anteriormente.
Esta atualização irá instalar a versão mais recente disponível `libcurl4-doc package` para o seu dispositivo.

   Em alternativa, pode selecionar o `sample-package-update-2-2.0.1-importManifest.json` ficheiro manifesto de importação e o ficheiro de `sample-2.0.1-libcurl4-doc-7.58-apt-manifest.json` atualização manifesto apta a partir da pasta que descarregou anteriormente. Isto irá instalar a versão específica v7.58.0 do `libcurl4-doc package` dispositivo.

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

1. Selecione a opção Atualizações de Dispositivos sob Gestão Automática de Dispositivos a partir da barra de navegação à esquerda.

1. Selecione o separador Grupos no topo da página.

1. Selecione o botão Adicionar para criar um novo grupo.

1. Selecione a etiqueta IoT Hub que criou no passo anterior da lista. Selecione Criar grupo de atualização.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot mostrando a seleção da etiqueta." lightbox="media/create-update-group/select-tag.PNG":::

[Saiba mais](create-update-group.md) sobre a adição de tags e criar grupos de atualização

## <a name="deploy-update"></a>Implementar atualização

1. Uma vez criado o grupo, deverá ver uma nova atualização disponível para o seu grupo de dispositivos, com uma ligação à atualização na coluna _de atualizações disponíveis._ Pode precisar de refrescar uma vez.

1. Clique no link para a atualização disponível.

1. Confirme que o grupo correto é selecionado como o grupo alvo e agende a sua implantação

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecione atualização" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Por predefinição, a data/hora de início é de 24 horas da sua hora atual. Certifique-se de selecionar uma data/hora diferente se quiser que a implementação comece mais cedo.

1. Selecione Implementar atualização.

1. Veja o gráfico de conformidade. Devia ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em curso" lightbox="media/deploy-update/update-in-progress.png":::

1. Depois de o seu dispositivo ter sido atualizado com sucesso, deverá ver a atualização do seu gráfico de conformidade e detalhes de implementação para refletir o mesmo. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização conseguiu" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorize uma implementação de atualização

1. Selecione o separador Implementações no topo da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Separador de implementações" lightbox="media/deploy-update/deployments-tab.png":::

1. Selecione a implementação criada para ver os detalhes da implementação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes da implementação" lightbox="media/deploy-update/deployment-details.png":::

1. Selecione Refresh para ver os mais recentes detalhes do estado. Continue este processo até que o estado mude para Sucesso.

Já concluiu uma atualização de pacotes de ponta a ponta com sucesso utilizando a Atualização do Dispositivo para IoT Hub num dispositivo Ubuntu Server 18.04 x64. 

## <a name="bonus-steps"></a>Passos de bónus

1. Repita as secções "Atualização de Importação" e "Implementar atualização"

3. Durante o passo "Import update", selecione o `sample-package-update-1.0.2-importManifest.json` ficheiro manifesto de importação e o ficheiro de `sample-1.0.2-libcurl4-doc-remove-apt-manifest.json` atualização manifesto apta da pasta que descarregou anteriormente. Esta atualização removerá o instalado `libcurl4-doc package` do seu dispositivo.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, limpe a conta de atualização do dispositivo, caso, o Hub IoT e o dispositivo IoT Edge (se tiver criado o VM através do botão Implementar para Azure). Pode fazê-lo, indo a cada recurso individual e selecionando "Delete". Tenha em atenção que é necessário limpar uma instância de atualização do dispositivo antes de limpar a conta de atualização do dispositivo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atualização de imagem no tutorial de Framboesa Pi 3 B+](device-update-raspberry-pi.md)
