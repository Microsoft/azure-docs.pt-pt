---
title: Instale a atualização no dispositivo da série Gateway da caixa de dados Azure | Microsoft Docs
description: Descreve como aplicar atualizações utilizando o portal Azure e o UI web local para o dispositivo da série Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 7fda10310a4bac085b248248d80d708dfa7f3ff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582765"
---
# <a name="update-your-azure-data-box-gateway"></a>Atualize o seu Gateway de Caixa de Dados Azure

Este artigo descreve os passos necessários para instalar a atualização no seu Gateway de Caixa de Dados Azure através da UI web local e através do portal Azure. Aplica as atualizações de software ou hotfixes para manter o seu dispositivo Data Box Gateway atualizado.

> [!IMPORTANT]
>
> - A atualização **1911** corresponde à versão de software **1.6.1049.786** no seu dispositivo. Para obter informações sobre esta atualização, aceda às [notas de lançamento](data-box-gateway-1911-release-notes.md).
>
> - Tenha em atenção que a instalação de uma atualização ou correção reinicia o dispositivo. Dado que o Data Box Gateway é um dispositivo de nó único, qualquer E/S em curso é interrompida e o dispositivo sofrerá um período de inatividade que poderá demorar até30 minutos para a atualização do software do dispositivo.

Cada um destes passos é descrito nas seguintes secções.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Recomendamos que instale atualizações através do portal Azure. O dispositivo verifica automaticamente uma vez por dia se existem atualizações. Assim que as atualizações estiverem disponíveis, vê uma notificação no portal. Em seguida, poderá transferir e instalar as atualizações.

> [!NOTE]
> Certifique-se de que o dispositivo está saudável e o estado mostra como **Online** antes de proceder à instalação das atualizações.

1. Quando as atualizações estão disponíveis para o seu dispositivo, vê uma notificação. Selecione a notificação ou a partir da barra de comando superior, **dispositivo de atualização**. Isto permitir-lhe-á aplicar atualizações de software de dispositivos.

    ![Screenshot da página inicial da Caixa de Dados Azure com as opções do dispositivo De visão geral e atualização chamadas.](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. Na lâmina de atualização do **Dispositivo,** verifique se reviu os termos da licença associados a novas funcionalidades nas notas de lançamento.

    Pode optar por **descarregar e instalar** as atualizações ou simplesmente **descarregar** as atualizações. Pode optar por instalar estas atualizações mais tarde.

    ![Screenshot do Dispositivo atualiza a caixa de diálogo com as opções Aceitar e Compreender e a opção descarregar e instalar chamada.](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    Se pretender descarregar e instalar as atualizações, verifique a opção que as atualizações instalam automaticamente após o download estar concluído.

    ![Screenshot do Dispositivo atualiza a caixa de diálogo com a opção Aceitar e a opção Download chamada.](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. O download de atualizações começa. Você vê uma notificação de que o download está em andamento.

    ![Screenshot da notificação que diz: "Descarregue e instale atualizações em andamento."](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    Um banner de notificação também é exibido no portal Azure. Isto indica o progresso do download.

    ![Screenshot da página inicial da Caixa de Dados Azure com a opção Overview e o banner de notificação de atualizações de descarregamento chamado.](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    Pode selecionar esta notificação ou selecionar **o dispositivo Update** para ver o estado detalhado da atualização.

    ![Screenshot do Download e instalação de atualizações caixa de diálogo com a mensagem Status: Em curso e a mensagem de atualizações de Descarregamento chamada.](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. Após o download estar concluído, o banner de notificação atualiza para indicar a conclusão. Se optar por descarregar e instalar as atualizações, a instalação começará automaticamente.

    ![Screenshot da página inicial da Caixa de Dados Azure com a opção Overview e o banner de notificação de atualizações descarregadas chamados.](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    Se optar por descarregar apenas atualizações, selecione a notificação para abrir a lâmina **de atualizações** do Dispositivo. Selecione **Instalar**.
  
    ![A screenshot do dispositivo atualiza a caixa de diálogo com a opção de instalação chamada.](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. Vê uma notificação de que a instalação está em andamento.

    ![Screenshot da página inicial da Caixa de Dados Azure com a opção Overview e a mensagem de descarregamento e instalação chamada.](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    O portal também apresenta um alerta informativo para indicar que a instalação está em andamento. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. Uma vez que se trata de um dispositivo de nó de 1, o dispositivo reiniciará após a instalação das atualizações. O alerta crítico durante o reinício indicará que o batimento cardíaco do dispositivo está perdido.

    ![Screenshot da página inicial da Caixa de Dados Azure com a opção Overview e o banner de notificação de alerta crítico chamado.](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    Selecione o alerta para ver o evento do dispositivo correspondente.

    ![Screenshot da secção de eventos do Dispositivo com o batimento cardíaco perdido do evento do seu dispositivo chamado.](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. O estado do dispositivo atualiza-se para **Online** após a instalação das atualizações.

    ![Screenshot da página inicial da Caixa de Dados Azure com a opção Overview e o estado online chamado.](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    A partir da barra de comando superior, selecione **atualizações do Dispositivo**. Verifique se a atualização foi instalada com sucesso e a versão do software do dispositivo reflete isso.

    ![Screenshot do Dispositivo atualiza a caixa de diálogo com a secção de versão de software instalada chamada.](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>Use a uI web local

Existem dois passos ao utilizar a uI web local:

- Descarregue a atualização ou o hotfix
- Instale a atualização ou o hotfix

Cada um destes passos é descrito em detalhe nas seguintes secções.

### <a name="download-the-update-or-the-hotfix"></a>Descarregue a atualização ou o hotfix

Execute os seguintes passos para descarregar a atualização. Pode descarregar a atualização a partir da localização fornecida pela Microsoft ou a partir do Catálogo de Atualizações da Microsoft.

Faça os seguintes passos para descarregar a atualização a partir do Catálogo de Atualizações da Microsoft.

1. Inicie o navegador e navegue para [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

   ![Screenshot do Catálogo de Atualização do Microsoft numa janela do navegador com data box gateway digitado na caixa de texto de pesquisa. Tanto a barra de endereços do navegador como a caixa de texto 'Procurar' são chamadas.](./media/data-box-gateway-apply-updates/download-update-1.png)

2. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, insira o número de Base de Conhecimento (KB) do hotfix ou termos para a atualização que pretende descarregar. Por exemplo, insira **o Gateway da Caixa de Dados Azure** e, em seguida, clique em **Procurar**.

   A lista geminada de atualização aparece como **Azure Data Box Gateway 1911**.

   ![Screenshot do Catálogo de Atualização do Microsoft numa janela do navegador com os resultados de pesquisa do Data Box Gateway apresentados e chamados.](./media/data-box-gateway-apply-updates/download-update-2.png)

3. Selecione **Transferir**. Existe um único ficheiro para descarregar chamado *SoftwareUpdatePackage.exe* que corresponde à atualização do software do dispositivo. Descarregue o ficheiro para uma pasta no sistema local. Também pode copiar a pasta para uma partilha de rede que seja acessível a partir do dispositivo.

   ![Screenshot da caixa de diálogo de descarregamento com o link para o ficheiro do ponto E X E do pacote de atualização de software chamado.](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da atualização ou instalação do hotfix, certifique-se de que:

- Tem a atualização ou o hotfix descarregado localmente no seu anfitrião ou acessível através de uma partilha de rede.
- O estado do seu dispositivo é saudável, como mostra a página **geral** da UI web local.

   ![Screenshot da web local De Gateway de Dados U I com a opção Dashboard e o estado do Software: Mensagem saudável chamada.](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

Este procedimento leva cerca de 20 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou hotfix.

1. Na uI web local, aceda à  >  **atualização do Software** de Manutenção . Tome nota da versão de software que está a executar.

   ![Screenshot da web local Descaixa de Dados Gateway U I com a opção de atualização de Software e a mensagem de versão de software Atual chamada.](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. Fornecer o caminho para o ficheiro de atualização. Também pode navegar no ficheiro de instalação da atualização se for colocado numa partilha de rede. Selecione o ficheiro de atualização de software com *SoftwareUpdatePackage.exe* sufixo.

   ![Screenshot do Explorador de Ficheiros com o ficheiro do ponto E X E do pacote de atualização de software.](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. Selecione **Aplicar**.

   ![Screenshot da web local de Data Box Gateway U I com a opção de atualização de software, a caixa de texto do caminho do ficheiro Update e a opção ''Apply Update' chamada.](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. Quando solicitado para confirmação, selecione **Sim** para prosseguir. Dado que o dispositivo é um único dispositivo de nó, após a aplicação da atualização, o dispositivo reinicia e há tempo de inatividade.

   ![Screenshot da caixa de diálogo de atualização de software com a opção Sim realçada.](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. A atualização começa. Depois de o dispositivo ser atualizado com sucesso, reinicia-o. A UI local não é acessível nesta duração.

6. Após o reinício, é levado para a página **Signo.** Para verificar se o software do dispositivo foi atualizado, na UI web local, aceda à  >  **atualização do Software** de Manutenção . A versão de software apresentada neste exemplo é **1.6.1049.786**.

   ![Screenshot da web local Descaixa de Dados Gateway U I com a opção de atualização de Software e a mensagem atualizada da versão do software Current chamada.](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu Gateway Azure Data Box](data-box-gateway-manage-users.md).
