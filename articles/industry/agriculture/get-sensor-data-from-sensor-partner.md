---
title: Obter dados de sensor dos parceiros
description: Descreve como obter dados de sensor de parceiros
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 380c67e5aeaba9be60e016f173e4da127e4e5f14
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798183"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Obter dados de sensor de parceiros de sensor

O Azure FarmBeats ajuda a trazer dados de streaming de dispositivos IoT e sensores para o Hub de dados. Atualmente, há suporte para os seguintes parceiros de dispositivo do sensor:

  ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

A integração de dados de dispositivo com o Azure FarmBeats ajuda você a obter dados de aterramento de sensores de IoT implantados em seu farm para o Hub de dados. Os dados, uma vez disponíveis, podem ser visualizados por meio do acelerador de FarmBeats e podem ser usados para a fusão de dados e criação de modelo de ia/ML usando o FarmBeats.

Para iniciar o streaming de dados do sensor, verifique o seguinte:

-  Você instalou o FarmBeats do Azure Marketplace.
-  Você decidiu sobre os sensores e dispositivos que deseja instalar em seu farm.
-  Se você estiver planejando usar sensores de umidade de solo, poderá usar o mapa de posicionamento do sensor de umidade de solo FarmBeats para obter uma recomendação sobre o número de sensores e onde deve colocar exatamente os sensores. Para obter mais informações, consulte [gerar mapas](generate-maps.md).

- Compre e implante dispositivos/sensores do seu parceiro de dispositivo em seu farm. Verifique se você pode acessar os dados do sensor por meio da solução de seus parceiros de dispositivo.

### <a name="enable-device-integration-with-farmbeats"></a>Habilitar a integração de dispositivos com o FarmBeats   

Depois de iniciar o streaming de dados do sensor, você pode iniciar o processo de obtenção dos dados para o sistema FarmBeats. Você precisa fornecer as seguintes informações ao seu provedor de dispositivo para habilitar a integração ao FarmBeats:  

 - Ponto Final de API  
 - ID do inquilino  
 - ID de Cliente  
 - Segredo do cliente  
 - Cadeia de conexão do EventHub

As informações acima são fornecidas a você pelo integrador de sistema. Para quaisquer problemas ao habilitar as integrações de dispositivo, entre em contato com o integrador de sistema.

Como alternativa, você pode gerar as credenciais executando esse script na Azure Cloud Shell. Siga as etapas abaixo:

1. Baixe o [arquivo zip](https://aka.ms/farmbeatspartnerscript) e extraia para a unidade local. Você encontrará dois arquivos dentro do arquivo ZIP.
2. Entre em https://portal.azure.com/ e Abra Cloud Shell (essa opção está disponível na barra superior direita do Portal)  

    ![Batidas no farm de projetos](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Verifique se o ambiente está definido como **PowerShell** -por padrão, ele está definido como bash.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Carregue os dois arquivos (da etapa 1 acima) em seu Cloud Shell.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Vá para o diretório onde os arquivos foram carregados (por padrão, eles são carregados no diretório base > nome de usuário).
6. Execute o seguintes script:

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```
7. Siga as instruções na tela para capturar os valores. (Ponto de extremidade da API, ID do locatário, ID do cliente, segredo do cliente e cadeia de conexão do EventHub).

**Integrar dados do dispositivo usando as credenciais geradas**

Visite o portal de parceiros de dispositivo para vincular FarmBeats usando o conjunto de credenciais que você gerou na seção anterior.

 - Ponto Final de API  
 - Cadeia de conexão do EventHub  
 - ID de Cliente  
 - Segredo do cliente  
 - ID do inquilino  

 O provedor do dispositivo confirma uma integração bem-sucedida. Após a confirmação, você pode exibir todos os dispositivos e sensores no Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Exibir dispositivos e sensores

Use a seção a seguir para exibir os dispositivos e sensores em seu farm.

### <a name="view-devices"></a>Ver dispositivos

Atualmente, o FarmBeats dá suporte aos seguintes dispositivos:

- **Nó**: um dispositivo ao qual um ou mais sensores estão anexados.
- **Gateway**: um dispositivo ao qual um ou mais sensores estão anexados.

Utilize os passos seguintes:

1. Na home page, selecione **dispositivos** no menu.
  A página dispositivos exibe o tipo de dispositivo, o modelo, o status, o farm no qual ele é colocado e a data da última atualização para os metadados. Por padrão, a coluna farm é definida como NULL. Você pode optar por atribuir um dispositivo a um farm. Para obter mais informações, consulte [atribuir dispositivos](#assign-devices).
2. Selecione o dispositivo para exibir as propriedades do dispositivo, telemetria e dispositivos filho conectados ao dispositivo.  

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Exibir sensores

Utilize os passos seguintes:

1. Na home page, selecione **sensores** no menu.
  A página sensores exibe detalhes sobre o tipo de sensor, o farm ao qual ele está conectado, o dispositivo pai, o nome da porta, o tipo de porta e o último status atualizado.
2. Selecione o sensor para exibir as propriedades do sensor, alertas ativos e telemetria do sensor.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Atribuir dispositivos  

Depois que os dados do sensor estiverem fluindo, você poderá atribuí-los ao farm no qual você implantou os sensores.

1. Na home page, selecione **farms** no menu, a página lista de **farms** é exibida.  
2. Selecione o farm ao qual você deseja atribuir o dispositivo e selecione **Adicionar dispositivos**.  
3. A janela **Adicionar dispositivos** é exibida. Selecione o dispositivo que você deseja atribuir ao farm.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Adicionar dispositivos**. Como alternativa, vá para o menu **dispositivos** , selecione os dispositivos que você deseja atribuir a um farm e selecione **associar dispositivos**.  
5. Na janela **associar dispositivos** , selecione o farm na lista suspensa e selecione **aplicar a todos** para associar o farm a todos os dispositivos selecionados.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Para associar cada dispositivo a um farm diferente, selecione a lista suspensa na coluna **atribuir ao farm** e selecione um farm para cada linha de dispositivo.  
7. Selecione **atribuir** para concluir a atribuição de dispositivo.

### <a name="visualize-sensor-data"></a>Visualizar dados do sensor

Utilize os passos seguintes:

1. Na home page, selecione **farms** no menu para exibir a página **farms** .  
2. Selecione o **farm** para o qual você deseja ver os dados do sensor.  
3. No painel do **farm** , você pode exibir dados de telemetria. Você pode optar por exibir a telemetria ao vivo ou usar o **intervalo personalizado** para exibir em um intervalo de datas específico.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>Excluir sensor

Siga estes passos.

1. Na home page, selecione **sensores** no menu para exibir a página **sensores** .  
2. Selecione o dispositivo que você deseja excluir e selecione **excluir** na janela de confirmação.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Uma mensagem de confirmação mostra que o sensor foi excluído com êxito.  

## <a name="delete-devices"></a>Excluir dispositivos

Siga estes passos.

1. Na home page, selecione **dispositivos** no menu para exibir a página dispositivos.  
2. Selecione o dispositivo que você deseja excluir e selecione **excluir** na janela de confirmação.

    ![Batidas no farm de projetos](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Passos seguintes

Agora você tem dados de sensor fluindo para sua instância de FarmBeats do Azure. Agora, saiba como [gerar mapas](generate-maps.md#generate-maps) para seus farms.
