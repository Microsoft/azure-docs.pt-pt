---
title: Obtenha dados de sensores dos parceiros
description: Este artigo descreve como obter dados de sensores de parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 113ab07af8ada16c0779da510c5f5b1f1f5a290b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398238"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Obtenha dados de sensores de parceiros de sensores

O Azure FarmBeats ajuda-o a trazer dados de streaming dos seus dispositivos IoT e sensores para o Datahub. Atualmente, os seguintes parceiros de dispositivos de sensor são suportados.

  ![Parceiros FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Integrar dados do dispositivo com o Azure FarmBeats ajuda-o a obter dados terrestres dos sensores IoT implantados na sua quinta para o centro de dados. Os dados, uma vez disponíveis, podem ser visualizados através do acelerador FarmBeats. Os dados podem ser utilizados para a fusão de dados e machine learning/inteligência artificial (ML/AI) utilizando farmBeats.

Para iniciar o streaming de dados dos sensores, certifique-se do seguinte:

-  Instalou FarmBeats no Azure Marketplace.
-  Decidiu nos sensores e dispositivos que pretende instalar na sua quinta.
-  Se planeia utilizar sensores de humidade do solo, utilize o mapa de colocação do sensor de humidade do solo FarmBeats para obter uma recomendação sobre o número de sensores e onde exatamente deve colocá-los. Para mais informações, consulte [Generate maps](generate-maps-in-azure-farmbeats.md).
- Compra e implanta dispositivos ou sensores do seu parceiro de dispositivo na sua quinta. Certifique-se de que pode aceder aos dados do sensor através da solução dos parceiros do seu dispositivo.

## <a name="enable-device-integration-with-farmbeats"></a>Ativar a integração de dispositivos com farmBeats

Depois de iniciar o streaming de dados do sensor, pode iniciar o processo de entrada dos dados no seu sistema FarmBeats. Forneça as seguintes informações ao seu fornecedor de dispositivos para permitir a integração na FarmBeats:

 - Ponto final de API
 - ID do inquilino
 - ID de Cliente
 - Segredo do cliente
 - Cadeia de ligação EventHub

Siga os passos abaixo para gerar a informação acima:

> [!NOTE]
> Estes passos são necessários para serem concluídos no Azure para aceder à subscrição do Azure onde o FarmBeats é implantado.

1. Inicie sessão em https://portal.azure.com/.

2. **Se estiver na versão 1.2.7 ou posterior do FarmBeats, salte os passos a, b e c, e vá para o passo 3.** Pode verificar a versão FarmBeats selecionando o ícone **Definições** no canto superior direito do UI FarmBeats.

      a.  Ir a Registos de > **Aplicações** de **Diretório Ativo azure**

      b. Selecione o Registo de **Aplicações** que foi criado como parte da sua implementação FarmBeats. Terá o mesmo nome que o seu centro de dados FarmBeats.

      c. Selecione **Expor um API** > selecione **Adicionar uma aplicação de cliente** e insira **04b07795-8ddb-461a-bbee-02f9e1bf7b46** e verifique **Autorizar o Scope**. Isto dará acesso ao Azure CLI (Cloud Shell) para realizar os passos abaixo:

3. Abra o Cloud Shell. Esta opção está disponível na barra de ferramentas no canto superior direito do portal Azure.

    ![Barra de ferramentas do portal Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Certifique-se de que o ambiente está definido para **powerShell**. Por defeito, está definido para Bash.

    ![Definição da barra de ferramentas PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Vá ao seu diretório de casa.

    ```azurepowershell-interactive 
    cd  
    ```

6. Execute o seguinte comando. Isto irá transferir um guião para o seu diretório em casa.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. Execute o seguinte script. O script pede o ID do Inquilino, que pode ser obtido a partir da página de > **visão geral** do **Diretório Ativo Azure.**

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. Siga as instruções no ecrã para capturar os valores para **API Endpoint,** **ID do inquilino,** **ID do cliente,** segredo do **cliente**e String **de Conexão EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrar os dados do dispositivo utilizando as credenciais geradas

Agora tem as seguintes informações geradas a partir da secção anterior.
 - Ponto final de API
 - Cadeia de ligação EventHub
 - ID de Cliente
 - Segredo do cliente
 - ID do inquilino

Terá de fornecer isto ao seu parceiro de dispositivo para ligar farmBeats. Vá ao portal do parceiro do dispositivo para fazer o mesmo. Por exemplo, caso esteja a utilizar dispositivos da Davis Instruments, Teralytic ou Pessl Instruments (Metos.at) para as páginas correspondentes, conforme mencionado abaixo:

1. [Instrumentos Davis](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralítico](https://app.teralytic.com/)

3. [Instrumentos pessl](https://ng.fieldclimate.com/user-api-services)

O fornecedor de dispositivos confirma uma integração bem sucedida. Após confirmação, pode ver todos os dispositivos e sensores no Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Ver dispositivos e sensores

Utilize a seguinte secção para visualizar os dispositivos e sensores para a sua exploração.

### <a name="view-devices"></a>Ver dispositivos

Atualmente, a FarmBeats suporta os seguintes dispositivos:

- **Nó**: Um dispositivo ao qual estão ligados um ou mais sensores.
- **Porta de entrada**: Um dispositivo ao qual um ou mais nós estão ligados.

Siga estes passos.

1. Na página inicial, selecione **Dispositivos** do menu.
  A página **Dispositivos** exibe o tipo de dispositivo, modelo, estado, a quinta onde está colocada e a última data atualizada para metadados. Por defeito, a coluna da quinta está definida para *NU*. Pode optar por atribuir um dispositivo a uma quinta. Para mais informações, consulte os [dispositivos De atribuição](#assign-devices).
2. Selecione o dispositivo para visualizar as propriedades do dispositivo, telemetria e dispositivos infantis ligados ao dispositivo.

    ![Página de dispositivos](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Ver sensores

Siga estes passos.

1. Na página inicial, selecione **Sensores** do menu.
  A página **Sensors** apresenta detalhes sobre o tipo de sensor, a quinta a que está ligado, dispositivo principal, nome da porta, tipo de porta, e o último estado atualizado.
2. Selecione o sensor para visualizar as propriedades do sensor, alertas ativos e telemetria a partir do sensor.

    ![Página de sensores](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Atribua dispositivos  

Depois de os dados do sensor terem fluído, pode atribuí-lo à quinta onde implantou os sensores.

1. Na página inicial, selecione **Quintas** do menu. A página da lista **de Quintas** aparece.
2. Selecione a quinta à qual pretende atribuir o dispositivo e selecione **Adicionar Dispositivos**.
3. Aparece a janela **Add Devices.** Selecione o dispositivo que pretende atribuir à quinta.

    ![Adicionar janela dispositivos](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Adicionar Dispositivos**. Em alternativa, vá ao menu **Dispositivos,** selecione os dispositivos que pretende atribuir a uma quinta e selecione **Dispositivos Associados**.
5. Na janela **Dispositivos Associados,** selecione a quinta a partir da lista de drop-down e selecione **Aplicar a Todos** para associar a quinta a todos os dispositivos selecionados.

    ![Janela de Dispositivos Associados](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Para associar cada dispositivo a uma quinta diferente, selecione a seta de entrega na coluna **Atributa à Quinta** e selecione uma exploração para cada linha de dispositivo.

7. **Selecione Atribuir** para completar a atribuição do dispositivo.

### <a name="visualize-sensor-data"></a>Visualizar dados de sensores

Siga estes passos.

1. Na página inicial, selecione **Quintas** do menu para ver a página **Quintas.**
2. Selecione a **Quinta** para a qual pretende ver os dados do sensor.
3. No painel da **Quinta,** pode ver dados de telemetria. Pode ver telemetria ao vivo ou usar **o Custom Range** para ver um intervalo de data específico.

    ![Painel de instrumentos de fazenda](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Apagar um sensor

Siga estes passos.

1. Na página inicial, selecione **Sensores** do menu para visualizar a página **Sensors.**
2. Selecione o dispositivo que pretende eliminar e selecione **Apagar** na janela de confirmação.

    ![Eliminar botão](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Uma mensagem de confirmação mostra que o sensor é eliminado com sucesso.

## <a name="delete-devices"></a>Eliminar dispositivos

Siga estes passos.

1. Na página inicial, selecione **Dispositivos** do menu para ver a página **dispositivos.**
2. Selecione o dispositivo que pretende eliminar e selecione **Apagar** na janela de confirmação.

    ![Eliminar botão](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Passos seguintes

Tem agora dados de sensores a fluir para a sua instância Azure FarmBeats. Agora, aprendam a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para as vossas quintas.
