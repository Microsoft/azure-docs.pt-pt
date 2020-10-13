---
title: Obtenha dados de sensores dos parceiros
description: Este artigo descreve como obter dados de sensores dos parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3452641b336308ddf46349064345b154e55aa53c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88649164"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Obtenha dados de sensores de parceiros de sensores

O Azure FarmBeats ajuda-o a trazer dados de streaming dos seus dispositivos e sensores IoT para o Datahub. Atualmente, os seguintes parceiros do dispositivo sensor são suportados.

  ![Parceiros FarmBeats](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

A integração dos dados dos dispositivos com o Azure FarmBeats ajuda-o a obter dados fundamentais dos sensores IoT implantados na sua quinta para o centro de dados. Os dados, uma vez disponíveis, podem ser visualizados através do acelerador FarmBeats. Os dados podem ser usados para a fusão de dados e para a aprendizagem automática/inteligência artificial (ML/AI) usando FarmBeats.

Para iniciar o streaming de dados do sensor, certifique-se do seguinte:

-  Instalou FarmBeats no Azure Marketplace.
-  Decidiu pelos sensores e dispositivos que pretende instalar na sua quinta.
-  Se planeia utilizar sensores de humidade do solo, utilize o mapa de colocação do sensor de humidade do solo FarmBeats para obter uma recomendação sobre o número de sensores e onde deve colocá-los. Para obter mais informações, consulte ['Gerar mapas'.](generate-maps-in-azure-farmbeats.md)
- Você compra e implanta dispositivos ou sensores do seu parceiro de dispositivo na sua fazenda. Certifique-se de que pode aceder aos dados do sensor através da solução dos parceiros do dispositivo.

## <a name="enable-device-integration-with-farmbeats"></a>Permitir a integração de dispositivos com FarmBeats

Depois de iniciar o streaming de dados de sensores, pode iniciar o processo de colocação dos dados no seu sistema FarmBeats. Forneça ao seu fornecedor de dispositivos as seguintes informações para permitir a integração ao FarmBeats:

 - Ponto final de API
 - ID do inquilino
 - ID de Cliente
 - Segredo do cliente
 - Cadeia de conexão EventHub

Siga os passos abaixo para gerar as informações acima:

> [!NOTE]
> Estes passos são necessários para serem concluídos no Azure para aceder à subscrição Azure onde farmbeats são implantados.

1. Inicie sessão em https://portal.azure.com/.

2. **Se estiver na versão 1.2.7 ou posterior do FarmBeats, salte os passos a, b e c, e vá para o passo 3.** Pode verificar a versão FarmBeats selecionando o ícone **Definições** no canto superior direito do FarmBeats UI.

      a.  Ir ao **Azure Ative Directory**  >  **App Registrs**

      b. Selecione o **Registo de Aplicações** que foi criado como parte da sua implementação FarmBeats. Terá o mesmo nome que o datahub do FarmBeats.

      c. **Selecione Expor uma API** > **selecionar Adicione uma aplicação ao cliente** e **introduza** **04b07795-8ddb-461a-bbee-02f9e1bf7b46** e verifique o Âmbito de Autorização . Isto dará acesso ao Azure CLI (Cloud Shell) para executar os passos abaixo:

3. Abra o Cloud Shell. Esta opção está disponível na barra de ferramentas no canto superior direito do portal Azure.

    ![Barra de ferramentas do portal Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Certifique-se de que o ambiente está definido para **PowerShell**. Por defeito, está definido para Bash.

    ![Definição da barra de ferramentas PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Vá para o seu diretório de casa.

    ```azurepowershell-interactive
    cd
    ```

6. Execute o seguinte comando. Isto liga uma conta autenticada para usar para pedidos AZure AD

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Execute o seguinte comando. Isto irá descarregar um script para o seu diretório de casa.

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Execute o seguinte script. O script pede o ID do inquilino, que pode ser obtido a partir da página de Visão Geral do **Diretório Ativo Azure.**  >  **Overview**

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

> [!NOTE]
> 1. O nome de Endpoint da API datahub deve estar em minúsculas.
> 2. Se estiver a copiar o url de nome do site farmbeats para Datahub API Endpoint, certifique-se de que não existe um corte de fuga (/).

9. Siga as instruções no ecrã para capturar os valores para **API Endpoint,** **ID do Inquilino,** **Identificação de Clientes,** **Segredo do Cliente**e Cadeia de **Conexão EventHub**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrar dados do dispositivo utilizando as credenciais geradas

Agora tem as seguintes informações geradas a partir da secção anterior.
 - Ponto final de API
 - Cadeia de conexão EventHub
 - ID de Cliente
 - Segredo do cliente
 - ID do inquilino

Terá de fornecer isto ao seu parceiro de dispositivo para ligar o FarmBeats. Vá ao portal do parceiro do dispositivo para fazer o mesmo. Por exemplo, caso utilize dispositivos da Davis Instruments, Teralytic ou Pessl Instruments (Metos.at) vá às páginas correspondentes, como mencionado abaixo:

1. [Instrumentos Davis](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Instrumentos pessis](https://ng.fieldclimate.com/user-api-services)

O fornecedor do dispositivo confirma uma integração bem sucedida. Após a confirmação, pode visualizar todos os dispositivos e sensores no Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Ver dispositivos e sensores

Utilize a seguinte secção para visualizar os dispositivos e sensores da sua quinta.

### <a name="view-devices"></a>Ver dispositivos

Atualmente, a FarmBeats suporta os seguintes dispositivos:

- **Nó**: Um dispositivo ao qual um ou mais sensores estão ligados.
- **Gateway**: Um dispositivo ao qual se ligam um ou mais nós.

Siga estes passos:

1. Na página inicial, selecione **Dispositivos** do menu.
  A página **dispositivos** exibe o tipo de dispositivo, modelo, estado, a fazenda onde foi colocada e a última data atualizada para metadados. Por predefinição, a coluna da exploração está definida como *NU .* Pode optar por atribuir um dispositivo a uma quinta. Para obter mais informações, consulte [dispositivos De atribuir](#assign-devices).
2. Selecione o dispositivo para visualizar as propriedades do dispositivo, a telemetria e os dispositivos infantis ligados ao dispositivo.

    ![Página de dispositivos](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Ver sensores

Siga estes passos:

1. Na página inicial, **selecione Sensores** do menu.
  A página **sensores** apresenta detalhes sobre o tipo de sensor, a quinta a que está ligado, dispositivo-mãe, nome da porta, tipo de porta e o último estado atualizado.
2. Selecione o sensor para visualizar as propriedades do sensor, alertas ativos e telemetria a partir do sensor.

    ![Página de sensores](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Atribuir dispositivos

Depois de os dados do sensor estiverem a fluir, pode atribuí-lo à quinta onde implantou os sensores.

1. Na página inicial, selecione **Farms** no menu. A página da lista **farms** aparece.
2. Selecione a quinta à qual pretende atribuir o dispositivo e selecione **Add Devices**.
3. Aparece a janela **Add Devices.** Selecione o dispositivo que pretende atribuir à quinta.

    ![Adicionar janela de dispositivos](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecione **Dispositivos de Adicionar**. Em alternativa, vá ao menu **Dispositivos,** selecione os dispositivos que pretende atribuir a uma quinta e selecione **Dispositivos Associados**.
5. Na janela **Dispositivos Associados,** selecione a quinta da lista de drop-down e selecione **Aplicar a Todos** para associar a quinta a todos os dispositivos selecionados.

    ![Janela de dispositivos associados](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Para associar cada dispositivo a uma quinta diferente, selecione a seta de queda na coluna **Atribua à Fazenda** e selecione uma quinta para cada linha do dispositivo.

7. Selecione **Atribuir** para completar a atribuição do dispositivo.

### <a name="visualize-sensor-data"></a>Visualizar dados de sensores

Siga estes passos:

1. Na página inicial, selecione **Quintas** no menu para ver a página **Farms.**
2. Selecione a **Quinta** para a qual deseja ver os dados do sensor.
3. No painel **farm,** pode ver os dados da telemetria. Pode ver telemetria ao vivo ou utilizar **o Alcance Personalizado** para ver um intervalo de datas específico.

    ![Painel de fazenda](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Apagar um sensor

Siga estes passos:

1. Na página inicial, selecione **Sensores** do menu para ver a página **dos Sensores.**
2. Selecione o dispositivo que pretende eliminar e selecione **Eliminar** na janela de confirmação.

    ![Eliminar botão](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Uma mensagem de confirmação mostra que o sensor é eliminado com sucesso.

## <a name="delete-devices"></a>Eliminar dispositivos

Siga estes passos:

1. Na página inicial, selecione **Dispositivos** do menu para ver a página **dispositivos.**
2. Selecione o dispositivo que pretende eliminar e selecione **Eliminar** na janela de confirmação.

    ![Eliminar botão](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Passos seguintes

Tem agora dados de sensores a fluir para a sua instância Azure FarmBeats. Agora, aprendam a [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para as vossas quintas.
