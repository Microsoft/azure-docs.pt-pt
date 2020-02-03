---
title: Obter dados de sensor dos parceiros
description: Este artigo descreve como obter dados de sensor de parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d56504c96c5e039f2563a1bfee577fe9b15e8563
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715574"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Obter dados de sensor de parceiros de sensor

O Azure FarmBeats ajuda a trazer dados de streaming de seus dispositivos e sensores de IoT para o Datahub. Atualmente, há suporte para os parceiros de dispositivo do sensor a seguir.

  ![Parceiros FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

A integração de dados de dispositivo com o Azure FarmBeats ajuda você a obter dados de aterramento de sensores de IoT implantados em seu farm para o Hub de dados. Os dados, uma vez disponíveis, podem ser visualizados por meio do acelerador de FarmBeats. Os dados podem ser usados para a fusão de dados e o modelo de aprendizado de máquina/inteligência artificial (ML/ia) usando o FarmBeats.

Para iniciar o streaming de dados do sensor, verifique o seguinte:

-  Você instalou o FarmBeats no Azure Marketplace.
-  Você decidiu sobre os sensores e dispositivos que deseja instalar em seu farm.
-  Se você planeja usar sensores de umidade de solo, use o mapa de posicionamento do sensor de umidade de solo FarmBeats para obter uma recomendação sobre o número de sensores e onde exatamente você deve colocá-los. Para mais informações, consulte [Generate maps](generate-maps-in-azure-farmbeats.md).
- Você compra e implanta dispositivos ou sensores do seu parceiro de dispositivo em seu farm. Verifique se você pode acessar os dados do sensor por meio da solução de seus parceiros de dispositivo.

## <a name="enable-device-integration-with-farmbeats"></a>Habilitar a integração de dispositivos com o FarmBeats

Depois de iniciar o streaming de dados do sensor, você pode começar o processo de obter os dados em seu sistema FarmBeats. Forneça as seguintes informações ao seu provedor de dispositivo para habilitar a integração ao FarmBeats:

 - Ponto de extremidade de API
 - ID do inquilino
 - ID de Cliente
 - Segredo do cliente
 - Cadeia de conexão do EventHub

Pode gerar a informação acima seguindo estes passos: (Por favor, note que estes passos são necessários para serem feitos no Azure para que você precise de acesso à subscrição Azure onde o FarmBeats é implantado)

1. Descarregue o [ficheiro zip](https://aka.ms/farmbeatspartnerscriptv2)e extrai-o para a sua unidade local. Haverá um arquivo dentro do arquivo zip.
2. Inscreva-se na https://portal.azure.com/ e vá ao Azure Ative Directory -> Registos de Aplicações

3. Clique no registro do aplicativo que foi criado como parte de sua implantação do FarmBeats. Ele terá o mesmo nome que o seu hub de dados FarmBeats.

4. Clique em "Expor um API" -> Clique em "Adicionar uma aplicação de cliente" e insira **04b07795-8ddb-461a-bbee-02f9e1bf7b46** e verifique "Autorizar o Scope". Isso dará acesso ao CLI do Azure (Cloud Shell) para executar as etapas a seguir.

5. Abra o Cloud Shell. Essa opção está disponível na barra de ferramentas no canto superior direito do portal do Azure.

    ![Barra de ferramentas portal do Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Certifique-se de que o ambiente está definido para **powerShell**. Por padrão, ele é definido como bash.

    ![Configuração da barra de ferramentas do PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Carregue o arquivo da etapa 1 em sua instância de Cloud Shell.

    ![Botão da barra de ferramentas carregar](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Vá para o diretório onde o arquivo foi carregado. Por padrão, os arquivos são carregados para o diretório base sob o nome de usuário.

9. Execute o seguinte script. O script solicita a ID do locatário que pode ser obtida na página de visão geral de Azure Active Directory >.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Siga as instruções no ecrã para capturar os valores para **API Endpoint,** **ID do inquilino,** **ID do cliente,** segredo do **cliente**e String **de Conexão EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrar dados do dispositivo usando as credenciais geradas

Agora tem as seguintes informações geradas a partir da secção anterior.
 - Ponto de extremidade de API
 - Cadeia de conexão do EventHub
 - ID de Cliente
 - Segredo do cliente
 - ID do inquilino
 
Terá de fornecer isto ao seu parceiro de dispositivo para ligar farmBeats. Vá ao portal do parceiro do dispositivo para fazer o mesmo. Por exemplo, caso esteja a utilizar dispositivos da Davis Instruments, por favor, vá para a página seguinte:

[Instrumentos Davis](https://weatherlink.github.io/azure-farmbeats/setup)

 O provedor do dispositivo confirma uma integração bem-sucedida. Após a confirmação, você pode exibir todos os dispositivos e sensores no Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Exibir dispositivos e sensores

Use a seção a seguir para exibir os dispositivos e sensores para seu farm.

### <a name="view-devices"></a>Ver dispositivos

Atualmente, o FarmBeats dá suporte aos seguintes dispositivos:

- **Nó**: Um dispositivo ao qual estão ligados um ou mais sensores.
- **Porta de entrada**: Um dispositivo ao qual um ou mais nós estão ligados.

Siga estes passos.

1. Na página inicial, selecione **Dispositivos** do menu.
  A página **Dispositivos** exibe o tipo de dispositivo, modelo, estado, a quinta onde está colocada e a última data atualizada para metadados. Por defeito, a coluna da quinta está definida para *NU*. Você pode optar por atribuir um dispositivo a um farm. Para mais informações, consulte os [dispositivos De atribuição](#assign-devices).
2. Selecione o dispositivo para exibir as propriedades do dispositivo, telemetria e dispositivos filho conectados ao dispositivo.

    ![Página de dispositivos](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Exibir sensores

Siga estes passos.

1. Na página inicial, selecione **Sensores** do menu.
  A página **Sensors** apresenta detalhes sobre o tipo de sensor, a quinta a que está ligado, dispositivo principal, nome da porta, tipo de porta, e o último estado atualizado.
2. Selecione o sensor para exibir as propriedades do sensor, os alertas ativos e a telemetria do sensor.

    ![Página de sensores](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Atribuir dispositivos  

Depois que os dados do sensor estiverem fluindo, você poderá atribuí-los ao farm no qual você implantou os sensores.

1. Na página inicial, selecione **Quintas** do menu. A página da lista **de Quintas** aparece.
2. Selecione a quinta à qual pretende atribuir o dispositivo e selecione **Adicionar Dispositivos**.
3. Aparece a janela **Add Devices.** Selecione o dispositivo que você deseja atribuir ao farm.

    ![Janela Adicionar dispositivos](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Adicionar Dispositivos**. Em alternativa, vá ao menu **Dispositivos,** selecione os dispositivos que pretende atribuir a uma quinta e selecione **Dispositivos Associados**.
5. Na janela **Dispositivos Associados,** selecione a quinta a partir da lista de drop-down e selecione **Aplicar a Todos** para associar a quinta a todos os dispositivos selecionados.

    ![Janela associar dispositivos](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Para associar cada dispositivo a uma quinta diferente, selecione a seta de entrega na coluna **Atributa à Quinta** e selecione uma exploração para cada linha de dispositivo.
7. **Selecione Atribuir** para completar a atribuição do dispositivo.

### <a name="visualize-sensor-data"></a>Visualizar dados do sensor

Siga estes passos.

1. Na página inicial, selecione **Quintas** do menu para ver a página **Quintas.**
2. Selecione a **Quinta** para a qual pretende ver os dados do sensor.
3. No painel da **Quinta,** pode ver dados de telemetria. Pode ver telemetria ao vivo ou usar **o Custom Range** para ver um intervalo de data específico.

    ![Painel do farm](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Excluir um sensor

Siga estes passos.

1. Na página inicial, selecione **Sensores** do menu para visualizar a página **Sensors.**
2. Selecione o dispositivo que pretende eliminar e selecione **Apagar** na janela de confirmação.

    ![Botão de eliminar](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Uma mensagem de confirmação mostra que o sensor foi excluído com êxito.

## <a name="delete-devices"></a>Excluir dispositivos

Siga estes passos.

1. Na página inicial, selecione **Dispositivos** do menu para ver a página **dispositivos.**
2. Selecione o dispositivo que pretende eliminar e selecione **Apagar** na janela de confirmação.

    ![Botão de eliminar](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Passos Seguintes

Agora você tem dados de sensor fluindo para sua instância de FarmBeats do Azure. Agora, aprendam a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para as vossas quintas.
