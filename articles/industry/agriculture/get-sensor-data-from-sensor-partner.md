---
title: Obter dados de sensor dos parceiros
description: Este artigo descreve como obter dados de sensor de parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7c40cfc8fbc6a9957281be842f6714fa9289a3c8
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122224"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Obter dados de sensor de parceiros de sensor

O Azure FarmBeats ajuda a trazer dados de streaming de seus dispositivos e sensores de IoT para o Datahub. Atualmente, há suporte para os parceiros de dispositivo do sensor a seguir.

  ![Parceiros FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

A integração de dados de dispositivo com o Azure FarmBeats ajuda você a obter dados de aterramento de sensores de IoT implantados em seu farm para o datahub. Os dados, uma vez disponíveis, podem ser visualizados por meio do acelerador de FarmBeats. Os dados podem ser usados para a fusão de dados e o modelo de aprendizado de máquina/inteligência artificial (ML/ia) usando o FarmBeats.

Para iniciar o streaming de dados do sensor, verifique o seguinte:

-  Você instalou o FarmBeats no Azure Marketplace.
-  Você decidiu sobre os sensores e dispositivos que deseja instalar em seu farm.
-  Se você planeja usar sensores de umidade de solo, use o mapa de posicionamento do sensor de umidade de solo FarmBeats para obter uma recomendação sobre o número de sensores e onde exatamente você deve colocá-los. Para obter mais informações, consulte [gerar mapas](generate-maps-in-azure-farmbeats.md).
- Você compra e implanta dispositivos ou sensores do seu parceiro de dispositivo em seu farm. Verifique se você pode acessar os dados do sensor por meio da solução de seus parceiros de dispositivo.

## <a name="enable-device-integration-with-farmbeats"></a>Habilitar a integração de dispositivos com o FarmBeats

Depois de iniciar o streaming de dados do sensor, você pode começar o processo de obter os dados em seu sistema FarmBeats. Forneça as seguintes informações ao seu provedor de dispositivo para habilitar a integração ao FarmBeats:

 - Ponto de extremidade de API
 - ID do inquilino
 - ID de Cliente
 - Segredo do cliente
 - Cadeia de conexão do EventHub

As informações anteriores são fornecidas a você pelo integrador de sistema. Para quaisquer problemas que você possa encontrar ao habilitar as integrações de dispositivo, entre em contato com o integrador de sistema.

Como alternativa, você pode gerar as credenciais executando esse script em Azure Cloud Shell. Siga estes passos.

1. Baixe o [arquivo zip](https://aka.ms/farmbeatspartnerscriptv2)e extraia-o para a unidade local. Haverá um arquivo dentro do arquivo zip.
2. Entre para https://portal.azure.com/ e vá para registros do aplicativo Azure Active Directory->

3. Clique no registro do aplicativo que foi criado como parte de sua implantação do FarmBeats. Ele terá o mesmo nome que o FarmBeats Datahub.

4. Clique em "expor uma API"-> clique em "adicionar um aplicativo cliente" e insira **04b07795-8ddb-461A-bbee-02f9e1bf7b46** e marque "autorizar escopo". Isso dará acesso ao CLI do Azure (Cloud Shell) para executar as etapas a seguir.

5. Abra o Cloud Shell. Essa opção está disponível na barra de ferramentas no canto superior direito do portal do Azure.

    ![Barra de ferramentas portal do Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Verifique se o ambiente está definido como **PowerShell**. Por padrão, ele é definido como bash.

    ![Configuração da barra de ferramentas do PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Carregue o arquivo da etapa 1 em sua instância de Cloud Shell.

    ![Botão da barra de ferramentas carregar](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Vá para o diretório onde o arquivo foi carregado. Por padrão, os arquivos são carregados para o diretório base sob o nome de usuário.

9. Execute o seguinte script. O script solicita a ID do locatário que pode ser obtida na página de visão geral de Azure Active Directory >.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Siga as instruções na tela para capturar os valores para **o ponto de extremidade da API**, ID do **locatário**, **ID do cliente**, segredo do **cliente**e cadeia de **conexão do EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrar dados do dispositivo usando as credenciais geradas

Acesse o portal de parceiros de dispositivo para vincular o FarmBeats usando o conjunto de credenciais que você gerou na seção anterior:

 - Ponto de extremidade de API
 - Cadeia de conexão do EventHub
 - ID de Cliente
 - Segredo do cliente
 - ID do inquilino

 O provedor do dispositivo confirma uma integração bem-sucedida. Após a confirmação, você pode exibir todos os dispositivos e sensores no Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Exibir dispositivos e sensores

Use a seção a seguir para exibir os dispositivos e sensores para seu farm.

### <a name="view-devices"></a>Ver dispositivos

Atualmente, o FarmBeats dá suporte aos seguintes dispositivos:

- **Nó**: um dispositivo ao qual um ou mais sensores estão anexados.
- **Gateway**: um dispositivo ao qual um ou mais nós estão anexados.

Siga estes passos.

1. Na home page, selecione **dispositivos** no menu.
  A página **dispositivos** exibe o tipo de dispositivo, o modelo, o status, o farm no qual ele é colocado e a data da última atualização para os metadados. Por padrão, a coluna farm é definida como *NULL*. Você pode optar por atribuir um dispositivo a um farm. Para obter mais informações, consulte [atribuir dispositivos](#assign-devices).
2. Selecione o dispositivo para exibir as propriedades do dispositivo, telemetria e dispositivos filho conectados ao dispositivo.

    ![Página de dispositivos](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Exibir sensores

Siga estes passos.

1. Na home page, selecione **sensores** no menu.
  A página **sensores** exibe detalhes sobre o tipo de sensor, o farm ao qual ele está conectado, dispositivo pai, nome da porta, tipo de porta e o último status atualizado.
2. Selecione o sensor para exibir as propriedades do sensor, os alertas ativos e a telemetria do sensor.

    ![Página de sensores](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Atribuir dispositivos  

Depois que os dados do sensor estiverem fluindo, você poderá atribuí-los ao farm no qual você implantou os sensores.

1. Na home page, selecione **farms** no menu. A página lista de **farms** é exibida.
2. Selecione o farm ao qual você deseja atribuir o dispositivo e selecione **Adicionar dispositivos**.
3. A janela **Adicionar dispositivos** é exibida. Selecione o dispositivo que você deseja atribuir ao farm.

    ![Janela Adicionar dispositivos](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Adicionar dispositivos**. Como alternativa, vá para o menu **dispositivos** , selecione os dispositivos que você deseja atribuir a um farm e selecione **associar dispositivos**.
5. Na janela **associar dispositivos** , selecione o farm na lista suspensa e selecione **aplicar a todos** para associar o farm a todos os dispositivos selecionados.

    ![Janela associar dispositivos](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Para associar cada dispositivo a um farm diferente, selecione a seta suspensa na coluna **atribuir ao farm** e selecione um farm para cada linha de dispositivo.
7. Selecione **atribuir** para concluir a atribuição de dispositivo.

### <a name="visualize-sensor-data"></a>Visualizar dados do sensor

Siga estes passos.

1. Na home page, selecione **farms** no menu para exibir a página **farms** .
2. Selecione o **farm** para o qual você deseja ver os dados do sensor.
3. No painel do **farm** , você pode exibir dados de telemetria. Você pode exibir a telemetria ao vivo ou usar o **intervalo personalizado** para ver um intervalo de datas específico.

    ![Painel do farm](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Excluir um sensor

Siga estes passos.

1. Na home page, selecione **sensores** no menu para exibir a página **sensores** .
2. Selecione o dispositivo que você deseja excluir e selecione **excluir** na janela de confirmação.

    ![Botão de eliminar](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Uma mensagem de confirmação mostra que o sensor foi excluído com êxito.

## <a name="delete-devices"></a>Excluir dispositivos

Siga estes passos.

1. Na home page, selecione **dispositivos** no menu para exibir a página **dispositivos** .
2. Selecione o dispositivo que você deseja excluir e selecione **excluir** na janela de confirmação.

    ![Botão de eliminar](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Passos seguintes

Agora você tem dados de sensor fluindo para sua instância de FarmBeats do Azure. Agora, saiba como [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para seus farms.
