---
title: Criar uma solução de aplicação distribuída geograficamente com o Azure e o Azure Stack | Documentos da Microsoft
description: Saiba como criar uma solução de aplicação distribuída geograficamente com o Azure e o Azure Stack.
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
ms.openlocfilehash: 299ea79a7a344a57bf302aae049ba87234427343
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442788"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>Tutorial: Criar uma solução de aplicação distribuída geograficamente com o Azure e o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como direcionar o tráfego para pontos de extremidade específicos com base em várias métricas de utilização do padrão de aplicações distribuídas geograficamente. Criação de um Gestor de tráfego de perfil com a configuração de encaminhamento e o ponto final baseadas na geografia garante que informações são encaminhadas para pontos finais com base em requisitos regionais, regulamento empresarial e internacional e suas necessidades de dados.

Neste tutorial, irá criar um ambiente de exemplo para:

> [!div class="checklist"]
> - Crie uma aplicação distribuída geograficamente.
> - Utilize o Gestor de tráfego para a aplicação de destino.

## <a name="use-the-geo-distributed-apps-pattern"></a>Usar o padrão de aplicações distribuída geograficamente

Com o padrão distribuída geograficamente, seu aplicativo pode se estende por regiões. Pode ser predefinido para a cloud pública, mas alguns dos utilizadores podem exigir que os seus dados permanecem na sua região. Pode direcionar os utilizadores para a cloud mais adequado com base nos seus requisitos.

### <a name="issues-and-considerations"></a>Problemas e considerações

#### <a name="scalability-considerations"></a>Considerações de escalabilidade

É a solução, que criará com este tutorial não acomodar a escalabilidade. No entanto, se for utilizado em combinação com outras tecnologias do Azure e no local e soluções capaz de acomodar os requisitos de escalabilidade. Para obter informações sobre como criar uma solução híbrida com o dimensionamento automático por meio de tráfego manager, consulte [criar soluções entre Clouds de dimensionamento com o Azure](azure-stack-solution-cloud-burst.md).

#### <a name="availability-considerations"></a>Considerações de disponibilidade

Como é o caso com considerações de escalabilidade, esta solução diretamente não abrange a disponibilidade. No entanto, também é semelhante à nossa considerações de escalabilidade, Azure e tecnologias no local e soluções podem ser implementadas dentro desta solução para garantir a elevada disponibilidade de todos os componentes envolvidos.

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão

- Sua organização possui ramificações internacionais que requerem segurança regional personalizada e políticas de distribuição.

- Cada um dos seus escritórios de organizações obtém os funcionários, a empresa e dados de instalação, exigindo a geração de relatórios de atividade por regulamentações locais e o fuso horário.

- Podem ser cumpridos os requisitos de escala elevada ao dimensionar horizontalmente as aplicações, com várias implementações de aplicações que está a ser efetuadas numa única região, bem como em várias regiões, para lidar com requisitos de carga extrema.

### <a name="planning-the-topology"></a>Planejar a topologia

Antes de criar um espaço de aplicação distribuída, ele ajuda os seguintes conceitos:

-   **Domínio personalizado para a aplicação:** O que é o nome de domínio personalizado que os clientes irão utilizar para aceder à aplicação? Para a aplicação de exemplo, o nome de domínio personalizado é *www.scalableasedemo.com.*

-   **Domínio do Gestor de tráfego:** Um nome de domínio tem de ser escolhido ao criar uma [perfil do Gestor de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Este nome será combinado com o *trafficmanager.net* sufixo para registar uma entrada de domínio que é gerenciada pelo Gestor de tráfego. Para a aplicação de exemplo, o escolhido de nome é *demonstração dimensionável ase*. Como resultado, é o nome de domínio completo que é gerido pelo Gestor de tráfego *demo.trafficmanager.net dimensionável ase*.

-   **Estratégia para dimensionar os requisitos de espaço de aplicação:** A superfície do aplicativo ser distribuída em vários ambientes de serviço de aplicações numa única região? Várias regiões? Uma combinação de ambas as abordagens? A decisão deve basear-se as expectativas de onde o tráfego do cliente terão origem, bem como a eficiência com que pode dimensionar o restante de uma aplicação que suporta a infraestrutura de back-end. Por exemplo, com uma aplicação sem monitoração de estado de 100%, uma aplicação pode ser altamente dimensionada utilizando uma combinação de vários ambientes de serviço de aplicações por região do Azure, multiplicado por ambientes de serviço de aplicação implementado em várias regiões do Azure. Com 15 + regiões globais do Azure disponíveis para escolha, os clientes verdadeiramente podem criar um volume de memória do aplicativo de hiper escala a nível mundial. Para a aplicação de exemplo utilizada neste artigo, três ambientes de serviço de aplicações foram criados numa única região do Azure (Sul E.u.a.).

-   **Convenção de nomenclatura para os ambientes de serviço de aplicações:** Cada ambiente de serviço de aplicações requer um nome exclusivo. Para além de um ou dois ambientes de serviço de aplicações, é útil ter uma convenção de nomenclatura para ajudar a identificar cada ambiente de serviço de aplicações. Para a aplicação de exemplo foi utilizada uma convenção de nomenclatura simple. Os nomes dos três ambientes de serviço de aplicação são *fe1ase*, *fe2ase*, e *fe3ase*.

-   **Convenção de nomenclatura para as aplicações:** Uma vez que várias instâncias da aplicação serão implementadas, é necessário um nome para cada instância da aplicação implementada. Com ambientes de serviço de aplicações, o mesmo nome de aplicação pode ser utilizado em vários ambientes de serviço de aplicações. Uma vez que cada ambiente de serviço de aplicações tem um sufixo de domínio exclusivo, os desenvolvedores podem optar por reutilizar o nome da aplicação mesmo exato em cada ambiente. Por exemplo, um desenvolvedor pode ter aplicações com o nome da seguinte forma: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc. Para a aplicação neste cenário, cada instância da aplicação tem um nome exclusivo. Os nomes de instância da aplicação utilizados são *webfrontend1*, *webfrontend2*, e *webfrontend3*.

> [!Tip]  
> ![pillars.png híbrida](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack é uma extensão do Azure. O Azure Stack coloca a agilidade e inovação da cloud de informática para o seu ambiente no local e ativar a única cloud híbrida que permite-lhe criar e implementar aplicações de híbridas em qualquer lugar.  
> 
> O White Paper [considerações de Design para aplicações híbridas](https://aka.ms/hybrid-cloud-applications-pillars) analisa pilares de qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para estruturar, implementar e utilizar aplicações híbridas. As considerações de design ajudam a otimizar o design de aplicações híbridas, minimizando desafios em ambientes de produção.

## <a name="part-1-create-a-geo-distributed-app"></a>Parte 1: Criar uma aplicação distribuída geograficamente

Nesta parte, irá criar uma aplicação web.

> [!div class="checklist"]
> - Criar aplicações web e publicar
> - Adicione o código para repositórios do Azure
> - Aponte a compilação de aplicação para vários destinos de cloud.
> - Gerir e configurar o processo de CD

### <a name="prerequisites"></a>Pré-requisitos

Uma subscrição do Azure e a instalação do Azure Stack são necessários.

### <a name="geo-distributed-app-steps"></a>Passos de aplicação distribuída geograficamente

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Obter um domínio personalizado e configurar o DNS

Atualize o ficheiro de zona DNS para o domínio. O Azure AD, em seguida, pode verificar a propriedade de nome de domínio personalizado. Uso [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para registos DNS do Azure/Office 365/externa no Azure, ou adicione a entrada DNS em [uma entidade de registo DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Registre-se um domínio personalizado com uma entidade de registo pública.

2. Inicie sessão na entidade de registo de nome de domínio para o domínio. Um administrador aprovado poderá ser necessário para disponibilizar as atualizações DNS.

3. Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pelo Azure AD. A entrada DNS não altera os comportamentos, como roteamento de emails ou hospedagem na web.

### <a name="create-web-apps-and-publish"></a>Criar aplicações web e publicar

Configure híbrida CI/CD para implementar aplicação Web no Azure e o Azure Stack e envie as alterações em ambas as nuvens de automático.

> [!Note]  
> O Azure Stack com as imagens apropriadas distribuídos para execução (Windows Server e SQL) e a implementação de serviço de aplicações são necessários. Consulte a documentação do serviço de aplicações [antes de começar com o serviço de aplicações no Azure Stack](../azure-stack-app-service-before-you-get-started.md) secção para o operador do Azure Stack.

#### <a name="add-code-to-azure-repos"></a>Adicione o código para repositórios do Azure

1. Inicie sessão no Visual Studio com uma **conta com direitos de criação de projeto** em repositórios do Azure.

    Pode aplicar híbrida a entrega contínua/integração contínua (CI/CD) de código do aplicativo e o código de infraestrutura. Uso [modelos Azure Resource Manager](https://azure.microsoft.com/resources/templates/) para ambos os desenvolvimento em nuvem alojada e privada.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **Clonar o repositório** ao criar e abrir a aplicação de web padrão.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Criar implementação da aplicação web em ambas as nuvens

1.  Editar a **WebApplication.csproj** ficheiro: Selecione **Runtimeidentifier** e adicione **win10 x64**. (Consulte [Self-contained implementação](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação.)

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Verificar o código para repositórios de Azure** usando o Team Explorer.

2.  Confirme que o **código da aplicação** foi verificado em repositórios do Azure.

### <a name="create-the-build-definition"></a>Criar a definição de compilação

1. **Inicie sessão no Azure Pipelines** para confirmar a capacidade de criar definições de compilação.

2. Adicione **win10-x64 - r** código. Isso é necessário para acionar uma implementação independente com .net Core.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image4.png)

3. **Executar a compilação**. O [compilação de implementação autónoma e contém](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo irá publicar artefactos que podem ser executadas no Azure e no Azure Stack.

**Utilização de um agente alojado do Azure**

Utilizar um agente de hospedado em Pipelines do Azure é uma opção conveniente para criar e implementar aplicações web. Atualizações e manutenção seja executadas automaticamente pelo Microsoft Azure, permitindo o desenvolvimento contínuo, sem interrupções, teste e implantação.

### <a name="manage-and-configure-the-cd-process"></a>Gerir e configurar o processo de CD

DevOps do Azure e o servidor de DevOps do Azure fornecem um pipeline totalmente configurável e gerenciável para versões em vários ambientes, tais como o desenvolvimento, teste, controle de qualidade e ambientes de produção; incluindo a exigência de aprovações nos estágios específicos.

#### <a name="create-release-definition"></a>Criar definição de versão


![Texto alternativo](media/azure-stack-solution-geo-distributed/image5.png)

1.  Selecione o **plus** botão para adicionar uma nova versão sob a **guia Releases** na página de criação e versão do Visual Studio Online (VSO).

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image6.png)

2. Aplicar a **implementação de serviço de aplicações do Azure** modelo.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image7.png)

3. No menu de pendente de artefacto de adicionar, **adicionar artefacto** para a aplicação de compilação na Cloud do Azure.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image8.png)

4. No separador do Pipeline, selecione o **fase, tarefas** ligação do ambiente e definir valores de ambiente de cloud do Azure.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image9.png)

5. Definir o **nome do ambiente** e selecione Azure **subscrição** para o ponto final de Cloud do Azure.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image10.png)

6. Em nome do ambiente, definir necessários **nome do serviço de aplicações do Azure**.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image11.png)

7. Introduza **Hosted VS2017** em fila de agente para o ambiente alojado na cloud do Azure.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image12.png)

8. No menu de implementar o serviço de aplicações do Azure, selecione o válido **pacote ou de pasta** para o ambiente. Selecione OK para **localização de pasta**.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image13.png)

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image14.png)

9. Guarde todas as alterações e voltar à **pipeline de lançamento**.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image15.png)

10. Adicionar uma **artefacto novo** selecionando a compilação para a aplicação do Azure Stack.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image16.png)

11. Adicionar uma aplicação de ambiente de mais o **implementação de serviço de aplicações do Azure.**

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image17.png)

12. Nomeie o novo ambiente **Azure Stack.**

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image18.png)

13. Localize o ambiente do Azure Stack sob **tarefa** separador.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image19.png)

14. Selecione o **subscrição** para o ponto de final do Azure Stack.

  ![Texto alternativo](media/azure-stack-solution-geo-distributed/image20.png)

15. Definir o nome da aplicação web do Azure Stack como o **nome do serviço de aplicações**.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image21.png)

16. Selecione o **agente do Azure Stack**.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image22.png)

17. Em serviço de aplicações do Azure de implementar secção selecione válido **pacote ou de pasta** para o ambiente. Selecione OK para **localização de pasta**.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image23.png)

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image24.png)

18. Sob **variável** separador Adicionar uma variável chamada `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, defina o respetivo valor como `true`e definir o âmbito para `Azure Stack`.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image25.png)

19. Selecione o **contínua** ícone de Acionador de implementação de artefactos e ativar o **Continues** acionador de implementação.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image26.png)

20. Selecione o **pré-implantação** ícone condições no ambiente do Azure Stack e defina o gatilho **depois do lançamento.**

21. Guarde todas as alterações.

> [!Note]  
>  Algumas definições para as tarefas podem ter sido automaticamente definidas como [variáveis de ambiente](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) ao criar uma definição de versão a partir de um modelo. Estas definições não podem ser modificadas nas definições de tarefa; em vez disso, tem de selecionar o item de ambiente de principal para editar essas configurações.

## <a name="part-2-update-web-app-options"></a>Parte 2: Atualizar opções de aplicação web

O [Serviço de Aplicações do Azure](https://docs.microsoft.com/azure/app-service/overview) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. 

![Texto alternativo](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Mapear um nome DNS personalizado já existente para as Aplicações Web do Azure
> - Utilize um * * gravador CNAME uma **um registo** para mapear um nome DNS personalizado no serviço de aplicações.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapear um nome DNS personalizado já existente para as Aplicações Web do Azure

> [!Note]  
>  Utilize um registo CNAME para todos os nomes DNS personalizados, exceto um domínio de raiz (para example,northwind.com).

Para migrar um site em direto e o respetivo nome de domínio DNS para o Serviço de Aplicações, veja [Migrate an active DNS name to Azure App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain) (Migrar um nome DNS ativo para o Serviço de Aplicações do Azure).

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

-   [Criar uma aplicação de serviço de aplicações](https://docs.microsoft.com/azure/app-service/), ou utilizar uma aplicação criada para outro tutorial.

-   Comprar um nome de domínio e garantir o acesso ao registo DNS para o fornecedor de domínio.

Atualize o ficheiro de zona DNS para o domínio. O Azure AD irá verificar a propriedade de nome de domínio personalizado. Uso [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para registos DNS do Azure/Office 365/externa no Azure, ou adicione a entrada DNS em [uma entidade de registo DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Registre-se um domínio personalizado com uma entidade de registo pública.

-   Inicie sessão na entidade de registo de nome de domínio para o domínio. (Poderá ser necessário um administrador aprovado para disponibilizar atualizações de DNS.)

-   Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pelo Azure AD.

Por exemplo, para adicionar entradas DNS para northwindcloud.com e www.northwindcloud.com, configure as definições de DNS para o domínio de raiz northwindcloud.com.

> [!Note]  
>  Um nome de domínio pode ser adquirido através do [portal do Azure](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).  
> Para mapear um nome DNS personalizado para uma aplicação Web, o [plano do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/) dessa aplicação tem de ser um escalão pago (**Partilhado**, **Básico**, **Standard** ou **Premium**).



### <a name="create-and-map-cname-and-a-records"></a>Criar e mapear CNAME e registos

#### <a name="access-dns-records-with-domain-provider"></a>Aceder a registos DNS com o fornecedor de domínios

> [!Note]  
>  Utilize o DNS do Azure para configurar um nome DNS personalizado para aplicações Web do Azure. Para obter mais informações, veja [Utilizar o DNS do Azure para oferecer definições de domínio personalizado para um serviço do Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain).

1.  Inicie sessão site do fornecedor principal de.

2.  Localize a página para gerir os registos DNS. Cada fornecedor de domínio tem sua própria interface de registos DNS. Procure áreas do site com os nomes **Nome de Domínio**, **DNS** ou **Gestão de Servidor de Nomes**.

Página de registos DNS pode ser visualizada no **meus domínios**. Localizar a ligação com o nome **ficheiro de zona**, **registos DNS**, ou **configuração avançada**.

A captura de ecrã seguinte mostra um exemplo de uma página de registos DNS:

![Página de registos DNS de exemplo](media/azure-stack-solution-geo-distributed/image28.png)

1.  Na entidade de registo de nome de domínio, selecione **adicionar ou criar** para criar um registo. Alguns fornecedores têm ligações diferentes para adicionar diferentes tipos de registos. Consulte a documentação do fornecedor.

2.  Adicione um registo CNAME para mapear um subdomínio para o nome de anfitrião da aplicação predefinido.

  No exemplo de domínio www.northwindcloud.com, adicione um registo CNAME que mapeia o nome para < aplicação\_nome >. azurewebsites.

Depois de adicionar o CNAME, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Navegação do portal para a aplicação do Azure](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Ativar o mapeamento de registos CNAME no Azure

1.  Num novo separador, inicie sessão no portal do Azure,

2.  Navegue para os serviços de aplicações.

3.  Selecione a aplicação web.

4.  No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **Domínios personalizados**.

5.  Selecione o ícone **+** junto a **Adicionar nome de anfitrião**.

1.  Escreva o nome de domínio completamente qualificado, como `www.northwindcloud.com`.

2.  Selecione **Validar**.

3.  Se indicado, adicione registos adicionais de outros tipos (`A` ou `TXT`) para os registos DNS de entidades de registo de nome de domínio. Azure irá fornecer os valores e os tipos destes registos:

    a.  Um registo **A**, para mapear o endereço IP da aplicação.

    b.  R **TXT** registo para mapear para o nome de anfitrião da aplicação predefinido < app_name >. azurewebsites. Serviço de aplicações utiliza este registo apenas no momento da configuração, para verificar a propriedade de domínio personalizado. Após a verificação, elimine o registo TXT.

4.  Concluir esta tarefa no separador de entidade de registo de domínio e revalide até que o **Adicionar nome de anfitrião** botão é ativado.

5.  Certifique-se de que * * tipo de registo de nome de anfitrião está definido como **CNAME (www.example.com ou qualquer subdomínio)**.

6.  Selecione **Adicionar nome de anfitrião**.

7.  Escreva o nome de domínio completamente qualificado, como `northwindcloud.com`.

8.  Selecione **Validar**.

9.  O **adicionar** é ativado.

10. Certifique-se de que * * tipo de registo de nome de anfitrião está definido como **um registo a (www.example.com)**.

11. **Adicionar nome de anfitrião**.

  Poderá demorar algum tempo para que os nomes de anfitrião novo sejam refletidas da aplicação **domínios personalizados** página. Experimente atualizar o browser para atualizar os dados.
  
  ![Texto alternativo](media/azure-stack-solution-geo-distributed/image31.png) 
  
  No caso de um erro, uma notificação de erro de verificação será exibida na parte inferior da página. ![Erro de verificação](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  Os passos acima podem ser repetidos para mapear um domínio de caráter universal (\*. northwindcloud.com).... Isso permite que a adição de quaisquer subdomínios adicionais para este serviço de aplicações sem ter de criar um registo CNAME separado para cada um deles. Siga as instruções de entidade de registo para configurar esta definição.

#### <a name="test-in-a-browser"></a>Testar no browser

Navegue para os nome ou nomes DNS configurados anteriormente (por exemplo, `northwindcloud.com`, www.northwindcloud.com.

## <a name="part-3-bind-a-custom-ssl-cert"></a>Parte 3: Vincular um certificado SSL personalizado

Nesta parte:

> [!div class="checklist"]
> - Vincular o certificado SSL personalizado no serviço de aplicações
> - Impor HTTPS para a aplicação
> - Automatizar o enlace de certificados SSL com scripts

> [!Note]  
> Se for necessário, obtenha um cliente certificado SSL no portal do Azure e vinculá-lo para a aplicação web. Siga o [tutorial App Service Certificates tutorial](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site) (Certificados do Serviço de Aplicações).

### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

-   [Criar uma aplicação do Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/)
-   [Mapear um nome DNS personalizado à sua aplicação Web](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Adquirir um certificado SSL de uma autoridade de certificação fidedigna e utilizar a chave para assinar o pedido

### <a name="requirements-for-your-ssl-certificate"></a>Requisitos do certificado SSL

Para utilizar um certificado no Serviço de Aplicações, aquele tem de cumprir os seguintes requisitos:

-   Ser assinado por uma autoridade de certificação fidedigna

-   Ser exportado como um ficheiro PFX protegido por palavra-passe

-   Conter uma chave privada com, pelo menos, 2048 bits de comprimento

-   Conter todos os certificados intermédios na cadeia de certificados

> [!Note]  
>  **Certificados de criptografia de curva (ECC) elíptica** trabalhar com o serviço de aplicações, mas não estão incluídos neste guia. Consulte uma autoridade de certificação para obter assistência na criação de certificados ECC. 

#### <a name="prepare-the-web-app"></a>Preparar a aplicação web

Para vincular um certificado SSL personalizado para a aplicação web, o [plano do App Service](https://azure.microsoft.com/pricing/details/app-service/) tem de estar no **básica**, **padrão**, ou **Premium** escalão.

#### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1.  Abra o [portal do Azure](https://portal.azure.com/) e navegue para a aplicação web.

2.  No menu à esquerda, selecione **dos serviços de aplicações**e, em seguida, selecione o nome da aplicação web.

![Selecionar a aplicação Web](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

1.  Na navegação do lado esquerda da página de aplicações web, desloque-se para o **configurações** secção e selecione **aumentar verticalmente (plano do serviço de aplicações)**.

    ![Menu de aumento vertical](media/azure-stack-solution-geo-distributed/image34.png)

1.  Certifique-se de que a aplicação web não está no **gratuito** ou **partilhado** escalão. Escalão atual da aplicação web está realçado numa caixa azul-escura.

    ![Verificar o escalão de preço](media/azure-stack-solution-geo-distributed/image35.png)

O SSL personalizado não é suportado nos escalões **Gratuito** e **Partilhado**. Para upscale, siga os passos na secção seguinte, ou **escolha o escalão de preço** página e avançar para o [carregar e vincular o certificado SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Aumentar verticalmente o seu plano do Serviço de Aplicações

1.  Selecione um dos escalões **Básico**, **Standard** ou **Premium**.

2.  Selecione **selecione**.

![Escolher um escalão de preço](media/azure-stack-solution-geo-distributed/image36.png)

A operação de dimensionamento está concluída quando a notificação é apresentada.

![Notificação para “aumentar verticalmente”](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Vincular o certificado SSL e intercalar certificados intermédios

Intercale vários certificados na cadeia.

1. **Abra cada certificado** recebido num editor de texto.

2. Crie um ficheiro para o certificado intercalado, denominado *mergedcertificate.crt*. Num editor de texto, copie o conteúdo de cada certificado para este ficheiro. A ordem dos seus certificados deve seguir a ordem na cadeia de certificados, a começar no seu certificado e a terminar no certificado de raiz. O aspeto é igual ao do exemplo abaixo:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o certificado SSL intercalado com a chave privada gerada pelo certificado.

Um ficheiro de chave privada é criado por meio de OpenSSL. Para exportar o certificado para PFX, execute o seguinte comando, substitua os marcadores de posição *< private-key-file >* e *< intercaladas-certificate-file >* com caminhos de chave privados e intercalados ficheiro de certificado.

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Quando lhe for pedido, defina uma palavra-passe de exportação para carregar o certificado SSL para o serviço de aplicações mais tarde.

Quando IIS ou **Certreq.exe** são utilizados para gerar o pedido de certificado, instale o certificado para um computador local e, em seguida [exportar o certificado para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Carregar o certificado SSL

1.  Selecione **definições de SSL** na navegação à esquerda da aplicação web.

2.  Selecione **carregar certificado**.

3.  Na **ficheiro de certificado PFX**, selecione ficheiro PFX.

4.  4. Na **palavra-passe do certificado**, escreva a palavra-passe que criou ao exportar o ficheiro PFX.

5.  Selecione **Upload**.

![Carregar certificado](media/azure-stack-solution-geo-distributed/image38.png)

Quando o serviço de aplicações concluir o carregamento do certificado, surge na **definições de SSL** página.

![Texto alternativo](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Vincular o seu certificado SSL

1.  Na **enlaces SSL** secção, selecione **Adicionar enlace**.

    > [!Note]  
    >  Se o certificado foi carregado, mas não aparece na nomes de domínio no **Hostname** lista pendente, tente atualizar a página do browser.

1.  Na **Adicionar enlace de SSL** página, utilize as listas pendentes para selecionar o nome de domínio para proteger e o certificado a utilizar.

2.  Na **tipo de SSL**, selecione se pretende utilizar [ **indicação de nome de servidor (SNI)**](http://en.wikipedia.org/wiki/Server_Name_Indication)ou SSL baseado em IP.

-   **SSL baseado em SNI**-enlaces SSL baseado em SNI várias podem ser adicionados. Esta opção permite utilizar vários certificados SSL para proteger múltiplos domínios no mesmo endereço IP. Os browsers mais modernos (incluindo o Internet Explorer, o Chrome, o Firefox e o Opera) suportam SNI (encontre informações mais abrangentes sobre o suporte de browsers em [Server Name Indication](http://wikipedia.org/wiki/Server_Name_Indication) [Indicação do Nome de Servidor]).

-   **SSL baseado em IP**-apenas um enlace SSL baseado em IP pode ser adicionado. Esta opção permite utilizar apenas um certificado SSL para proteger um endereço IP público dedicado. Proteger vários domínios, protegê-los usando o mesmo certificado SSL. Esta é a opção tradicional para o enlace de SSL.

    1.  Selecione **Adicionar enlace**.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image40.png)

Quando o serviço de aplicações concluir o carregamento do certificado, surge na **enlaces SSL** secções.

![Texto alternativo](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Remapeie o registo para o IP SSL

Se o SSL baseado em IP não é utilizado na aplicação web, avance para o [testar HTTPS para o seu domínio personalizado](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Por predefinição, a aplicação web utiliza um endereço IP público partilhado. Quando o certificado está vinculado com SSL baseado em IP, o serviço de aplicações cria um endereço IP novo e dedicado para a aplicação web.

Quando um registo é mapeado para a aplicação web, o registo de domínio têm de ser atualizado com o endereço IP dedicado.

O **Custom domain** página é atualizada com o endereço IP dedicado novo. Copie esta [endereço IP](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), remapear, em seguida, o [um registo](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) para este endereço IP novo.

#### <a name="test-https"></a>Tester HTTPS

Em vários browsers, navegue até https://<your.custom.domain>to, certifique-se da aplicação web é atendida.

![Texto alternativo](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> Se ocorrerem erros de validação de certificado, um certificado autoassinado pode ser a causa ou certificados intermédios poderão ter ficados fora ao exportar para o ficheiro PFX.

#### <a name="enforce-https"></a>Impor HTTPS

Por padrão, qualquer pessoa pode aceder na aplicação web com HTTP. todos os pedidos HTTP para a porta HTTPS podem ser redirecionados.

Na página da aplicação web, selecione **definições de SL**. Em seguida, em **HTTPS Apenas**, selecione **Ativado**.

![Impor HTTPS](media/azure-stack-solution-geo-distributed/image43.png)

Quando a operação estiver concluída, navegue para um dos URLs HTTP que apontam para a aplicação. Por exemplo:

-   http://<app_name>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Impor TLS 1.1/1.2

A aplicação permite [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 por predefinição, o que é já não é considerada segura pelas normas do setor, tais como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões do TLS superiores, siga estes passos:

1.  Na página da aplicação web, na navegação à esquerda, selecione **definições de SSL**.

2.  Na **versão do TLS**, selecione a versão mínima do TLS.

![Impor TLS 1.1 ou 1.2](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

1.  Selecione **criar um recurso** > **rede** > **perfil do Traffic Manager** > **criar**.

2.  No painel **Criar perfil do Gestor de Tráfego**, preencha o seguinte:

    1.  Na **nome**, forneça um nome para o perfil. Este nome tem de ser exclusivo dentro da zona de manager.net de tráfego e resultados no nome do DNS, manager.net de tráfego, o que é usado para acessar o perfil do Gestor de tráfego.

    2.  Na **método de encaminhamento**, selecione a **método Geographicrouting**.

    3.  Na **subscrição**, selecione a subscrição sob a qual pretende criar este perfil.

    4.  Em **Grupo de Recursos**, crie um grupo de recursos novo onde colocar este perfil.

    5.  Em **Localização do grupo de recursos**, selecione a localização do grupo de recursos. Esta definição refere-se para a localização do grupo de recursos e não tem impacto sobre o perfil do Gestor de tráfego que vai ser implementado globalmente.

    6.  Selecione **Criar**.

    7.  Quando a implementação global do perfil do Gestor de tráfego estiver concluída, é apresentada no respetivo grupo de recursos como um dos recursos.

    ![Texto alternativo](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

1.  Na barra de pesquisa de portais, procure o * * perfil do Traffic Manager * * nome criado na secção anterior e selecione o perfil do Gestor de tráfego nos resultados que apresentados.

2.  Na **perfil do Gestor de tráfego**, na **definições** secção, selecione **pontos finais**.

3.  Selecione **Adicionar**.

4.  A adicionar o ponto de final do Azure Stack.

5.  Para **tipo**, selecione **ponto final externo**.

6.  Fornecer um **nome** para este ponto final, o ideal é que o nome da pilha do Azure.

7.  Para o nome de domínio completamente qualificado (**FQDN**), o URL externo de utilização da aplicação de Web do Azure Stack.

8.  Em mapeamento geográfico, selecione um continente/região onde o recurso está localizado, por exemplo, **Europa.**

9.  Em país/região pendente que é apresentada, selecione o país que serão aplicadas a este ponto final, por exemplo, **Alemanha**.

10. Mantenha a caixa **Adicionar como desativado** desmarcada.

11. Selecione **OK**.

12. Adicionar o ponto final do Azure:

    1.  Para **tipo**, selecione **ponto final do Azure**.

    2.  Fornecer um **nome** para este ponto final.

    3.  Para **tipo de recurso de destino**, selecione **serviço de aplicações**.

    4.  Para **recurso de destino**, selecione **escolher um serviço de aplicações** para mostrar a lista das aplicações Web na mesma subscrição. Na **recursos**, escolha a utilização do serviço de aplicações como o primeiro ponto final.

13. Em mapeamento geográfico, selecione um continente/região onde o recurso está localizado, por exemplo, **América de América do Norte/Central/Caraíbas.**

14. Sob o país/região pendente que aparece, deixe em branco para selecionar todo o agrupamento regional acima.

15. Mantenha a caixa **Adicionar como desativado** desmarcada.

16. Selecione **OK**

  > [!Note]  
  >  Crie, pelo menos, um ponto final com um âmbito geográfico de todas as (mundo) para ser usado como o ponto final predefinido para o recurso.

1.  Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados em **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

  ![Texto alternativo](media/azure-stack-solution-geo-distributed/image46.png)

**Empresa global baseia-se nas capacidades do Azure a distribuição geográfica**

Direcionar o tráfego de dados através do Gestor de tráfego do Azure e geografia os pontos finais permite às empresas globais cumpra as normas regionais e manter os dados seguros e não compatíveis crucial para o sucesso dos negócios locais e em localizações remotas.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre os padrões de Cloud do Azure, veja [padrões de conceção de Cloud](https://docs.microsoft.com/azure/architecture/patterns).
