---
title: Criar soluções entre Clouds de dimensionamento com o Azure | Documentos da Microsoft
description: Saiba como criar soluções entre Clouds de dimensionamento com o Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: bcb538cf2e546e4913ccb223e143c2343e114de7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896199"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>Tutorial: Criar soluções entre Clouds de dimensionamento com o Azure

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como criar uma solução entre Clouds para fornecer um processo manualmente acionado para alternar de um Azure Stack alojada a aplicação web, a Azure alojada a aplicação web com o dimensionamento automático através do Gestor de tráfego, garantindo o utilitário de nuvem flexível e dimensionável quando sob carga.

Com esse padrão, o inquilino pode não ser pronto para executar a sua aplicação na cloud pública. No entanto, pode não ser economicamente viável para as empresas e manter a capacidade necessária no respetivo ambiente no local para lidar com picos de procura para a aplicação. O inquilino pode demorar utilizar a elasticidade da cloud pública com a respetiva solução no local.

Neste tutorial, irá criar um ambiente de exemplo para:

> [!div class="checklist"]
> - Crie uma aplicação web de vários nós.
> - Configurar e gerir o processo de implementação contínua (CD).
> - Publicar a aplicação web no Azure Stack.
> - Crie uma versão.
> - Saiba como monitorizar e controlar as suas implementações.

> [!Tip]  
> ![pillars.png híbrida](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack é uma extensão do Azure. O Azure Stack coloca a agilidade e inovação da cloud de informática para o seu ambiente no local e ativar a única cloud híbrida que permite-lhe criar e implementar aplicações de híbridas em qualquer lugar.  
> 
> O White Paper [considerações de Design para aplicações híbridas](https://aka.ms/hybrid-cloud-applications-pillars) analisa pilares de qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para estruturar, implementar e utilizar aplicações híbridas. As considerações de design ajudam a otimizar o design de aplicações híbridas, minimizando desafios em ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

-   Subscrição do Azure. Se necessário, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um sistema integrado do Azure Stack ou a implementação do Development Kit do Azure Stack.
    - Encontrará instruções para instalar o Azure Stack na [instalar o Kit de desenvolvimento do Azure Stack](../asdk/asdk-install.md).
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Esta instalação pode exigir algumas horas a concluir.

-   Implementar [serviço de aplicações](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) serviços de PaaS para o Azure Stack.

-   [Criar plano/ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dentro do ambiente do Azure Stack.

-   [Criar subscrição de inquilino](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro do ambiente do Azure Stack.

-   Crie uma aplicação Web dentro da subscrição de inquilino. Tome nota do novo URL de aplicação Web para utilizar mais tarde.

-   Implemente a máquina de Virtual de Pipelines do Azure dentro da subscrição de inquilino.

-   VM Windows Server 2016 com o .NET 3.5 necessário. Esta VM será criado na subscrição do inquilino no Azure Stack como o agente de compilação privada.

-   [Windows Server 2016 com a imagem de VM do SQL 2017](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image#add-a-vm-image-through-the-portal) está disponível no mercado de pilha do Azure. Se esta imagem não estiver disponível, trabalhe com um operador de pilha do Azure para se certificar de que é adicionado ao ambiente.

## <a name="issues-and-considerations"></a>Problemas e considerações

### <a name="scalability-considerations"></a>Considerações de escalabilidade

O componente principal do dimensionamento entre Clouds é a capacidade de fornecer a dimensionar de imediato, a pedido entre público e infraestrutura de cloud, serviço consistente e confiável a provar conforme prescrito pela intimação no local.

### <a name="availability-considerations"></a>Considerações de disponibilidade

Certifique-se localmente as aplicações implementadas estão configuradas para elevada disponibilidade através de implementação de software e configuração de hardware do local.

### <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

A solução entre Clouds garante gestão totalmente integrada e uma interface familiar entre ambientes. PowerShell recomenda-se para a gestão de várias plataformas.

## <a name="cross-cloud-scaling"></a>Dimensionamento entre Clouds

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Obter um domínio personalizado e configurar o DNS

Atualize o ficheiro de zona DNS para o domínio. O Azure AD irá verificar a propriedade de nome de domínio personalizado. Uso [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para registos DNS do Azure/Office 365/externa no Azure, ou adicione a entrada DNS em [uma entidade de registo DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Registre-se um domínio personalizado com uma entidade de registo pública.

2.  Inicie sessão na entidade de registo de nome de domínio para o domínio. Um administrador aprovado poderá ser necessário para disponibilizar atualizações de DNS. 

3.  Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pelo Azure AD. (A entrada DNS não irá afetar roteamento de emails ou comportamentos de alojamento na web.) 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Criar uma aplicação web de vários nós predefinida no Azure Stack

Configurar a integração contínua híbrida e implementação contínuas (CI/CD) para implementar aplicação Web no Azure e o Azure Stack e envie as alterações em ambas as nuvens de automático.

> [!Note]  
> O Azure Stack com as imagens apropriadas distribuídos para execução (Windows Server e SQL) e a implementação de serviço de aplicações são necessários. Consulte a documentação do serviço de aplicações "[antes de começar com o serviço de aplicações no Azure Stack](../azure-stack-app-service-before-you-get-started.md)" secção para o operador do Azure Stack.

### <a name="add-code-to-azure-repos"></a>Adicione o código para repositórios do Azure

Repositórios do Azure

1. Inicie sessão no Azure repositórios com uma conta que tenha direitos de criação do projeto nos repositórios do Azure.

    Pode aplicar híbrida CI/CD de código do aplicativo e o código de infraestrutura. Uso [modelos Azure Resource Manager](https://azure.microsoft.com/resources/templates/) para ambos os desenvolvimento em nuvem alojada e privada.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image1.JPG)

2. **Clonar o repositório** ao criar e abrir a aplicação de web padrão.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Criar implementação da aplicação web autônomo para serviços de aplicações em ambas as nuvens

1.  Editar a **WebApplication.csproj** ficheiro. Selecione **Runtimeidentifier** e adicione **win10 x64**. (Consulte [Self-contained implementação](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação.) 

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image3.png)

2.  Verifique o código para repositórios de Azure com o Team Explorer.

3.  Confirme que o código da aplicação foi verificado em repositórios do Azure.

## <a name="create-the-build-definition"></a>Criar a definição de compilação

1. Definições de compilação do registo em Pipelines do Azure para confirmar a capacidade de criar.

2. Adicione **win10-x64 - r** código. Isso é necessário para acionar uma implementação independente com .NET Core.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image4.png)

3. Execute a compilação. O [compilação de implementação autónoma e contém](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo irá publicar artefactos que podem ser executadas no Azure e no Azure Stack.

## <a name="use-an-azure-hosted-agent"></a>Utilização do Azure alojada agente

Uso um agente alojado nos Pipelines do Azure é uma opção conveniente para criar e implementar aplicações web. Atualizações e manutenção seja executadas automaticamente pelo Microsoft Azure, permitindo o desenvolvimento contínuo, sem interrupções, teste e implantação.

### <a name="manage-and-configure-the-cd-process"></a>Gerir e configurar o processo de CD

Servidor de DevOps do Azure e Pipelines do Azure fornecem um pipeline totalmente configurável e gerenciável para versões em vários ambientes, tais como o desenvolvimento, teste, controle de qualidade e ambientes de produção; incluindo a exigência de aprovações nos estágios específicos.

## <a name="create-release-definition"></a>Criar definição de versão

![Texto alternativo](media/azure-stack-solution-cloud-burst/image5.png)

1.  Selecione o **plus** botão para adicionar uma nova versão sob a **guia Releases** na página de criação e versão do VSO.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image6.png)

2. Aplica o modelo de implementação de serviço de aplicações do Azure.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image7.png)

3. Em Adicionar artefacto, adicione o artefacto da aplicação de compilação na Cloud do Azure.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image8.png)

4. No separador do Pipeline, selecione o **fase, tarefas** ligação do ambiente e definir valores de ambiente de cloud do Azure.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image9.png)

5. Definir o **nome do ambiente** e selecione Azure **subscrição** para o ponto final de Cloud do Azure.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image10.png)

6. Em nome do ambiente, definir necessários **nome do serviço de aplicações do Azure**.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image11.png)

7. Introduza **Hosted VS2017** em fila de agente para o ambiente alojado na cloud do Azure.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image12.png)

8. No menu de implementar o serviço de aplicações do Azure, selecione o válido **pacote ou de pasta** para o ambiente. Selecione **OK** ao **localização da pasta**.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image13.png)

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image14.png)

9. Guarde todas as alterações e voltar à **pipeline de lançamento**.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image15.png)

10. Adicione um artefacto novos, selecionando a compilação para a aplicação do Azure Stack.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image16.png)

11. Adicione um ambiente mais aplicar a implementação de serviço de aplicações do Azure.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image17.png)

12. Nomeie o novo ambiente do Azure Stack.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image18.png)

13. Localize o ambiente do Azure Stack sob **tarefa** separador.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image19.png)

14. Selecione a subscrição para o ponto de final do Azure Stack.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image20.png)

15. Defina o nome da aplicação web do Azure Stack como o nome do serviço de aplicações.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image21.png)

16. Selecione o agente do Azure Stack.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image22.png)

17. Em serviço de aplicações do Azure de implementar secção selecione válido **pacote ou de pasta** para o ambiente. Selecione **OK** para localização de pasta.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image23.png)

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image24.png)

18. No separador variável, adicione uma variável chamada `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, defina o respetivo valor como **true**e o escopo para o Azure Stack.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image25.png)

19. Selecione o **contínua** ícone de Acionador de implementação de artefactos e ativar o **Continues** acionador de implementação.

    ![Texto alternativo](media/azure-stack-solution-cloud-burst/image26.png)

20. Selecione o **pré-implantação** ícone condições no ambiente do Azure Stack e defina o gatilho **depois do lançamento.**

21. Guarde todas as alterações.

> [!Note]  
> Algumas definições para as tarefas podem ter sido automaticamente definidas como [variáveis de ambiente](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) ao criar uma definição de versão a partir de um modelo. Estas definições não podem ser modificadas nas definições de tarefa; em vez disso, o item de ambiente de principal tem de ser selecionado para editar essas configurações

## <a name="publish-to-azure-stack-via-visual-studio"></a>Publicar no Azure Stack através do Visual Studio

Através da criação de pontos de extremidade, uma compilação do Visual Studio Online (VSTO) pode implementar aplicações de serviço do Azure para o Azure Stack. Pipelines do Azure liga-se para o agente de compilação, que liga ao Azure Stack.

1.  Inicie sessão para o VSTO e navegue para a página de definições.

2.  No **configurações**, selecione **segurança**.

3.  Na **grupos de VSTS**, selecione **criadores de ponto final**.

4.  Sobre o **membros** separador, selecione **Add**.

5.  Na **adicionar utilizadores e grupos**, introduza um nome de utilizador e selecione o que o utilizador da lista de utilizadores.

6.  Selecione **guardar alterações**.

7.  Na **grupos de VSTS** lista, selecione **administradores do ponto final**.

8.  Sobre o **membros** separador, selecione **Add**.

9.  Na **adicionar utilizadores e grupos**, introduza um nome de utilizador e selecione o que o utilizador da lista de utilizadores.

10. Selecione **guardar alterações**.

Agora que as informações de ponto final existem, os Pipelines do Azure para a ligação do Azure Stack está pronto a utilizar. O agente de compilação no Azure Stack obtém as instruções de Pipelines do Azure e, em seguida, o agente transmite informações de ponto final para comunicação com o Azure Stack.

## <a name="develop-the-application-build"></a>Desenvolver a compilação de aplicação

> [!Note]  
> O Azure Stack com as imagens apropriadas distribuídos para execução (Windows Server e SQL) e a implementação de serviço de aplicações são necessários. Consulte a documentação do serviço de aplicações "[antes de começar com o serviço de aplicações no Azure Stack](../azure-stack-app-service-before-you-get-started.md)" secção para o operador do Azure Stack.

Uso [modelos do Azure Resource Manager, como web](https://azure.microsoft.com/resources/templates/) dos repositórios do Azure para implementar em ambas as nuvens de código da aplicação.

### <a name="add-code-to-an-azure-repos-project"></a>Adicione o código para um projeto de Repos do Azure

1.  Inicie sessão no Azure repositórios com uma conta que tenha direitos de criação do projeto no Azure Stack. Captura de ecrã seguinte mostra como ligar ao projeto HybridCICD.

2.  **Clonar o repositório** ao criar e abrir a aplicação de web padrão.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Criar implementação da aplicação web autônomo para serviços de aplicações em ambas as nuvens

1.  Editar a **WebApplication.csproj** ficheiro: Selecione **Runtimeidentifier** e, em seguida, adicione o win10 x64. Para obter mais informações, consulte [implementação autónoma e contém](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação.

2.  Utilize o Team Explorer para verificar o código em repositórios do Azure.

3.  Confirme que o código da aplicação foi verificado em repositórios do Azure.

### <a name="create-the-build-definition"></a>Criar a definição de compilação

1.  Inicie sessão no Azure Pipelines com uma conta que pode criar uma definição de compilação.

2.  Navegue para o **Criar aplicação Web** página para o projeto.

3.  Na **argumentos**, adicione **win10-x64 - r** código. Isto é necessário para acionar uma implementação independente com .NET Core.

4.  Execute a compilação. O [compilação de implementação autónoma e contém](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo irá publicar artefactos que podem ser executadas no Azure e no Azure Stack.

#### <a name="use-an-azure-hosted-build-agent"></a>Utilização do Azure alojada agente de compilação

Utilizar um agente de compilação hospedado em Pipelines do Azure é uma opção conveniente para a criação e implementação de aplicações web. Atualizações e manutenção de agente seja executadas automaticamente pelo Microsoft Azure, que permite que um ciclo de desenvolvimento contínuo e sem interrupções.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurar o processo de implementação contínua (CD)

Servidor de DevOps do Azure e Pipelines do Azure fornecem um pipeline totalmente configurável e gerenciável para as versões em vários ambientes, tais como o desenvolvimento, teste, controle de qualidade (QA) e a produção. Esse processo pode incluir a necessidade de aprovações, específicos estágios do ciclo de vida do aplicativo.

#### <a name="create-release-definition"></a>Criar definição de versão

A criação de uma definição de versão é o processo de compilação a etapa final no aplicativo. Esta definição de versão é utilizada para criar uma versão e implementar uma compilação.

1.  Inicie sessão para os Pipelines do Azure e navegue para **criar e lançar** para o projeto.

2.  Sobre o **versões** separador, selecione **[+]** e, em seguida, escolha **Criar definição de versão**.

3.  No **selecionar um modelo**, escolha **implementação de serviço de aplicações do Azure**e, em seguida, selecione **aplicar**.

4.  No **adicionar artefacto**, da **origem (definição de compilação)** selecione a aplicação de compilação na Cloud do Azure.

5.  Sobre o **Pipeline** separador, selecione a **1 fase**, **1 tarefa** ligar a **ver tarefas do ambiente**.

6.  Na **tarefas** separador, introduza Azure como o **nome do ambiente** e selecione EP de Traders Web AzureCloud do **subscrição do Azure** lista.

7.  Introduza o **nome do serviço de aplicações do Azure**, que é `northwindtraders` na captura de ecrã seguinte.

8.  Para a fase de agente, selecione **Hosted VS2017** partir do **fila de agente** lista.

9.  Na **implementar serviço de aplicações do Azure**, selecione o válido **pacote ou de pasta** para o ambiente.

10. Na **selecione o ficheiro ou pasta**, selecione **OK** para **localização**.

11. Guarde todas as alterações e voltar à **Pipeline**.

12. Na **Pipeline** separador, selecione **adicionar artefacto**e selecione o **NorthwindCloud Traders-Vessel** do **origem (definição de compilação)** lista.

13. No **selecionar um modelo**, adicionar outro ambiente. Escolher **implementação de serviço de aplicações do Azure** e, em seguida, selecione **aplicar**.

14. Introduza `Azure Stack` como o **nome do ambiente**.

15. Sobre o **tarefas** separador, localize e selecione o Azure Stack.

16. Partir do **subscrição do Azure** lista, selecione **AzureStack Traders Vessel EP** para o ponto de final do Azure Stack.

17. Introduza o nome da aplicação web do Azure Stack como o **nome do serviço de aplicações**.

18. Sob **seleção de agente**, escolha **AzureStack -b Douglas preencha** do **fronta Agenta** lista.

19. Para **implementar serviço de aplicações do Azure**, selecione o válido **pacote ou de pasta** para o ambiente. No **selecionar ficheiro ou pasta**, selecione **OK** para a pasta **localização**.

20. Sobre o **variável** separador, encontre a variável chamada `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Defina o valor da variável para **true**e defina seu escopo para **do Azure Stack**.

21. Sobre o **Pipeline** separador, selecione a **acionador de implementação contínua** ícone para o artefacto de NorthwindCloud Traders-Web e o conjunto a **acionador de implementação contínua** para **Ativada**. Fazer a mesma coisa o **NorthwindCloud Traders-Vessel** artefacto.

22. Para o ambiente do Azure Stack, selecione o **condições de pré-implantação** ícone defina o gatilho **após o lançamento**.

23. Guarde todas as alterações.

> [!Note]  
> Algumas definições para tarefas de lançamento são definidas automaticamente como [variáveis de ambiente](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) ao criar uma definição de versão a partir de um modelo. Estas definições não podem ser modificadas nas definições de tarefa, mas podem ser modificadas de itens de ambiente principais.

## <a name="create-a-release"></a>Criar uma versão

1.  Na **Pipeline** separador, abra o **versão** lista e escolha **criar versão**.

2.  Introduza uma descrição para a versão, verifique que os artefactos corretos estão selecionados e, em seguida, escolha **criar**. Após alguns instantes, é apresentada uma faixa que indica que foi criada a nova versão e o nome de versão é apresentado como um link. Escolha a ligação para ver a página de resumo de lançamento.

3.  A página de resumo de lançamento para mostra detalhes sobre a versão. Na captura de ecrã seguinte para "Release-2", o **ambientes** secção mostra o **estado de implementação** para o Azure como "Em curso" e o estado para o Azure Stack é "concluído com êxito". Quando o estado de implementação para o ambiente do Azure é alterado para "Êxito", é apresentada uma faixa que indica que a versão está pronta para aprovação. Quando uma implementação está pendente ou falhou, uma azul **(i)** é mostrado o ícone de informações. Coloque o cursor sobre o ícone para ver um pop-up que contém o motivo do atraso ou falha.

4.  Outras exibições, como a lista de versões, também apresentar um ícone que indica a aprovação está pendente. O pop-up para este ícone mostra o nome do ambiente e obter mais detalhes relacionados com a implementação. É fácil para um administrador, consulte o progresso global de versões e veja que versões estão a aguardar aprovação.

## <a name="monitor-and-track-deployments"></a>Monitorar e controlar Implantações

1.  Sobre o **versão 2** página resumida, selecione **registos**. Durante uma implantação, esta página mostra o registo em direto do agente. O painel esquerdo apresenta o estado de cada operação na implementação de cada ambiente.

2.  Selecione um ícone de pessoa no **ação** coluna para uma aprovação de pré-implantação ou pós-implementação para ver quem aprovado (ou rejeitado) a implementação e a mensagem fornecidos por eles.

3.  Após a conclusão da implementação, todo o arquivo de log é apresentado no painel da direita. Selecione qualquer **passo** no painel da esquerda para ver o ficheiro de registo para uma única etapa, tal como **inicializar tarefa**. A capacidade de ver os registos individuais torna mais fácil rastrear e depurar as partes da implementação geral. **Guarde** o ficheiro de registo para um passo, ou **transferir os registos de todas as jako zip**.

4.  Abra o **resumo** separador para ver informações gerais sobre o lançamento. Esta vista mostra detalhes sobre a compilação, os ambientes que foi implantado, estado de implementação e outras informações sobre a versão.

5.  Selecione uma ligação de ambiente (**Azure** ou **do Azure Stack**) para ver informações sobre existente e implementações para um ambiente específico pendentes. Utilize estas vistas como uma forma rápida de verificar que a mesma compilação foi implementada para os dois ambientes.

6.  Abra o **implementado a aplicação de produção** no browser. Por exemplo, para o Web site dos serviços de aplicações do Azure, abra o URL [http://[nome da aplicação\]. azurewebsites.net](http:// [your-app-name].azurewebsites.net).

**A integração do Azure e o Azure Stack oferece uma solução dimensionável de entre Clouds**

Segurança de dados, novamente é um serviço de várias cloud flexível e robusto fornece cópia de segurança e redundância, disponibilidade rápida e consistente, armazenamento escalável e distribuição e encaminhamento em conformidade com a georreplicação. Este processo manualmente acionado garante carga confiável e eficiente, alternar entre as aplicações Web alojadas, garantindo a disponibilidade imediata de dados fundamentais. 

## <a name="next-steps"></a>Passos Seguintes
- Para saber mais sobre os padrões de Cloud do Azure, veja [padrões de conceção de Cloud](https://docs.microsoft.com/azure/architecture/patterns).
