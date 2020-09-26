---
title: Instalar atualização no dispositivo GPU Azure Stack Edge Pro / Microsoft Docs
description: Descreve como aplicar atualizações usando o portal Azure e web UI local para o dispositivo GPU Azure Stack Edge Pro e o cluster Kubernetes no dispositivo
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/24/2020
ms.author: alkohli
ms.openlocfilehash: 8b733d1e5eae9cae01233fda4be078bd188e07c9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334366"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Atualize o seu GPU Azure Stack Edge Pro 

Este artigo descreve os passos necessários para instalar a atualização no Azure Stack Edge Pro com GPU através da UI web local e através do portal Azure. Aplica as atualizações de software ou hotfixes para manter o dispositivo Azure Stack Edge Pro e o cluster Kubernetes associado no dispositivo atualizado. 

O procedimento descrito neste artigo foi realizado usando uma versão diferente do software, mas o processo permanece o mesmo para a versão atual do software.

> [!IMPORTANT]
> - A atualização **de 2009** corresponde à versão de software **2.1.1358.2075** no seu dispositivo. Para obter informações sobre esta atualização, aceda às [notas de lançamento](azure-stack-edge-gpu-2009-release-notes.md).
>
> - Tenha em atenção que a instalação de uma atualização ou correção reinicia o dispositivo. Esta atualização requer que aplique duas atualizações sequencialmente. Primeiro aplica-se as atualizações de software do dispositivo e depois as atualizações de Kubernetes. Dado que o Azure Stack Edge Pro é um único dispositivo de nó, qualquer I/S em andamento é interrompido e o seu dispositivo experimenta um tempo de inatividade de até 30 minutos para a atualização do software do dispositivo.

Para instalar atualizações no seu dispositivo, primeiro tem de configurar a localização do servidor de atualização. Depois de configurar o servidor de atualização, pode aplicar as atualizações através do portal Azure UI ou da UI web local.

Cada um destes passos é descrito nas seguintes secções.

## <a name="configure-update-server"></a>Servidor de atualização configurado

1. Na UI web local, aceda ao servidor **De Atualização de Configuração**  >  **Update server**. 
   
    ![Configure atualizações](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. No **tipo de servidor de atualização Select**, a partir da lista de dropdown, escolha entre o servidor Microsoft Update (padrão) ou o Windows Server Update Services.  
   
    Se atualizar a partir dos Serviços de Atualização do Servidor do Windows, especifique o servidor URI. O servidor desse URI irá implementar as atualizações em todos os dispositivos ligados a este servidor.

    ![Configure atualizações](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    O servidor WSUS é utilizado para gerir e distribuir atualizações através de uma consola de gestão. Um servidor WSUS também pode ser a fonte de atualização para outros servidores WSUS dentro da organização. O servidor WSUS que age como uma origem de atualização chama-se um servidor a montante. Numa implementação WSUS, pelo menos um servidor WSUS na sua rede deve ser capaz de se conectar ao Microsoft Update para obter informações de atualização disponíveis. Como administrador, pode determinar - com base na segurança e configuração da rede - quantos outros servidores WSUS se ligam diretamente ao Microsoft Update.
    
    Para mais informações, aceda aos [Serviços de Atualização do Servidor do Windows (WSUS)](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Recomendamos que instale atualizações através do portal Azure. O dispositivo verifica automaticamente as atualizações uma vez por dia. Assim que as atualizações estiverem disponíveis, vê uma notificação no portal. Em seguida, pode descarregar e instalar as atualizações. 

> [!NOTE]
> Certifique-se de que o dispositivo está saudável e o estado mostra como **Online** antes de proceder à instalação das atualizações.

1. Quando as atualizações estão disponíveis para o seu dispositivo, vê uma notificação. Selecione a notificação ou a partir da barra de comando superior, **dispositivo de atualização**. Isto permitir-lhe-á aplicar atualizações de software de dispositivos.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. Na lâmina de atualização do **Dispositivo,** verifique se reviu os termos da licença associados a novas funcionalidades nas notas de lançamento.

    Pode optar por **descarregar e instalar** as atualizações ou simplesmente **descarregar** as atualizações. Pode optar por instalar estas atualizações mais tarde.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-2a.png)    

    Se pretender descarregar e instalar as atualizações, verifique a opção que as atualizações instalam automaticamente após o download estar concluído.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-2b.png)

3. O download de atualizações começa. Você vê uma notificação de que o download está em andamento.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Um banner de notificação também é exibido no portal Azure. Isto indica o progresso do download. 

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Pode selecionar esta notificação ou selecionar **o dispositivo Update** para ver o estado detalhado da atualização.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)   


4. Após o download estar concluído, o banner de notificação atualiza para indicar a conclusão. Se optar por descarregar e instalar as atualizações, a instalação começará automaticamente.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Se optar por descarregar apenas atualizações, selecione a notificação para abrir a lâmina **de atualizações** do Dispositivo. Selecione **Install** (Instalar).
  
    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. Vê uma notificação de que a instalação está em andamento.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)

    O portal também apresenta um alerta informativo para indicar que a instalação está em andamento. O aparelho desliga-se e encontra-se em modo de manutenção.
    
    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Uma vez que se trata de um dispositivo de nó de 1, o dispositivo reiniciará após a instalação das atualizações. O alerta crítico durante o reinício indicará que o batimento cardíaco do dispositivo está perdido.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Selecione o alerta para ver o evento do dispositivo correspondente.
    
    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. Após o reinício, o dispositivo é novamente colocado no modo de manutenção e é apresentado um alerta informativo para indicar isso.

    Se selecionar o **dispositivo 'Actualização'** a partir da barra de comando superior, poderá ver o progresso das atualizações.   

8. O estado do dispositivo atualiza-se para **Online** após a instalação das atualizações. 

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    A partir da barra de comando superior, selecione **atualizações do Dispositivo**. Verifique se a atualização foi instalada com sucesso e a versão do software do dispositivo reflete isso.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

9. Verá novamente uma notificação de que as atualizações estão disponíveis. Estas são as atualizações de Kubernetes. Selecione o dispositivo de atualização ou **selecione a** partir da barra de comando superior.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. Descarregue as atualizações de Kubernetes. Pode ver que o tamanho do pacote é diferente quando comparado com o pacote de atualização anterior.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    O processo de instalação é idêntico ao das atualizações do dispositivo. Primeiro as atualizações são descarregadas.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. Uma vez descarregadas as atualizações, pode instalar as atualizações. 

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    À medida que as atualizações são instaladas, o dispositivo é colocado em modo de manutenção. O dispositivo não reinicia para as atualizações de Kubernetes. 

    Uma vez instaladas as atualizações de Kubernetes com sucesso, a notificação do banner desaparece, uma vez que não são necessárias mais atualizações. O seu dispositivo tem agora a mais recente versão do software do dispositivo e da Kubernetes.

    ![Versão de software após atualização](./media/azure-stack-edge-gpu-install-update/portal-update-20.png)


## <a name="use-the-local-web-ui"></a>Use a uI web local

Existem dois passos ao utilizar a uI web local:

* Descarregue a atualização ou o hotfix
* Instale a atualização ou o hotfix

Cada um destes passos é descrito em detalhe nas seguintes secções.


### <a name="download-the-update-or-the-hotfix"></a>Descarregue a atualização ou o hotfix

Execute os seguintes passos para descarregar a atualização. Pode descarregar a atualização a partir da localização fornecida pela Microsoft ou a partir do Catálogo de Atualizações da Microsoft.


Faça os seguintes passos para descarregar a atualização a partir do Catálogo de Atualizações da Microsoft.

1. Inicie o navegador e navegue para [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Catálogo de pesquisa](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, insira o número de Base de Conhecimento (KB) do hotfix ou termos para a atualização que pretende descarregar. Por exemplo, insira **Azure Stack Edge Pro**e, em seguida, clique em **Procurar**.
   
    A lista geminada de atualização aparece como **Azure Stack Edge Pro 2006**.
   
    ![Catálogo de pesquisa](./media/azure-stack-edge-gpu-install-update/download-update-2b.png)

4. Selecione **Transferir**. Existem dois ficheiros para descarregar com * sufixosSoftwareUpdatePackage.exe* e *Kubernetes_Package.exe* que correspondem a atualizações de software de dispositivos e atualizações de Kubernetes, respectivamente. Descarregue os ficheiros para uma pasta no sistema local. Também pode copiar a pasta para uma partilha de rede que seja acessível a partir do dispositivo.

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da atualização ou instalação do hotfix, certifique-se de que:

 - Tem a atualização ou o hotfix descarregado localmente no seu anfitrião ou acessível através de uma partilha de rede.
 - O estado do seu dispositivo é saudável, como mostra a página **geral** da UI web local.

   ![atualizar o dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png) 

Este procedimento leva cerca de 20 minutos para ser concluído. Execute os seguintes passos para instalar a atualização ou hotfix.

1. Na uI web local, **Maintenance**aceda à  >  **atualização do Software**de Manutenção . Tome nota da versão de software que está a executar. 
   
   ![atualizar o dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Fornecer o caminho para o ficheiro de atualização. Também pode navegar no ficheiro de instalação da atualização se for colocado numa partilha de rede. Selecione o ficheiro de atualização de software com *SoftwareUpdatePackage.exe* sufixo.

   ![atualizar o dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-3a.png)

3. Selecione **Aplicar**. 

   ![atualizar o dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Quando solicitado para confirmação, selecione **Sim** para prosseguir. Dado que o dispositivo é um único dispositivo de nó, após a aplicação da atualização, o dispositivo reinicia e há tempo de inatividade. 
   
   ![atualizar o dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. A atualização começa. Depois de o dispositivo ser atualizado com sucesso, reinicia-o. A UI local não é acessível nesta duração.
   
6. Após o reinício, é levado para a página **Signo.** Para verificar se o software do dispositivo foi atualizado, **Maintenance**na UI web local, aceda à  >  **atualização do Software**de Manutenção . A versão de software apresentada neste exemplo é **2.0.1257.1591**.

   ![atualizar o dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png) 

7. Irá agora atualizar a versão de software Kubernetes. Repita os passos acima. Forneça um caminho para o ficheiro de atualização kubernetes com o sufixo *Kubernetes_Package.exe.*  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)--> 

8. Selecione **Aplicar**. 

   ![atualizar o dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Quando solicitado para confirmação, selecione **Sim** para prosseguir. 

10. Após a instalação da atualização Kubernetes com sucesso, não **Maintenance**há alteração no software exibido na  >  **atualização do Software**de Manutenção . 


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).
