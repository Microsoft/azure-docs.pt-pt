---
title: Implantar um aplicativo SaaS de banco de dados multilocatário fragmentado
description: Implante e explore o aplicativo de banco de dados multilocatário SaaS da Wingtip tickets, que demonstra os padrões de SaaS usando o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
ms.date: 10/16/2018
ms.openlocfilehash: 3277318e01362df8fc21ff7ca769aaeb8006abc6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827999"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Implantar e explorar um aplicativo multilocatário fragmentado

Neste tutorial, você implanta e explora um aplicativo SaaS multilocatário de exemplo chamado Wingtip tickets. O aplicativo Wingtip tickets foi projetado para demonstrar recursos do banco de dados SQL do Azure que simplificam a implementação de cenários de SaaS.

Esta implementação do aplicativo Wingtip tickets usa um padrão de banco de dados multilocatário fragmentado. A fragmentação é por identificador de locatário. Os dados do locatário são distribuídos para um banco de dado específico de acordo com os valores do identificador do locatário. 

Esse padrão de banco de dados permite que você armazene um ou mais locatários em cada fragmento ou banco de dados. Você pode otimizar o custo mais baixo, fazendo com que cada banco de dados seja compartilhado por vários locatários. Ou você pode otimizar o isolamento fazendo com que cada banco de dados armazene apenas um locatário. Sua opção de otimização pode ser feita independentemente para cada locatário específico. Sua escolha pode ser feita quando o locatário é armazenado pela primeira vez ou você pode mudar de ideia mais tarde. O aplicativo foi projetado para funcionar bem de qualquer forma.

## <a name="app-deploys-quickly"></a>O aplicativo é implantado rapidamente

O aplicativo é executado na nuvem do Azure e usa o banco de dados SQL do Azure. A seção de implantação a seguir fornece o botão azul **implantar no Azure** . Quando o botão é pressionado, o aplicativo é totalmente implantado em sua assinatura do Azure dentro de cinco minutos. Você tem acesso completo para trabalhar com os componentes individuais do aplicativo.

O aplicativo é implantado com dados para três locatários de exemplo. Os locatários são armazenados juntos em um banco de dados de vários locatários.

Qualquer pessoa pode baixar C# o e o código-fonte do PowerShell para Wingtip tickets em [seu repositório GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Aprenda neste tutorial

> [!div class="checklist"]
> - Como implantar o aplicativo SaaS Wingtip tickets.
> - Onde obter o código-fonte do aplicativo e os scripts de gerenciamento.
> - Sobre os servidores e bancos de dados que compõem o aplicativo.
> - Como os locatários são mapeados para seus dados com o *Catálogo*.
> - Como provisionar um novo locatário.
> - Como monitorar a atividade de locatário no aplicativo.

Há uma série de tutoriais relacionados disponíveis que se baseiam nessa implantação inicial. Os tutoriais exploram uma variedade de padrões de design e gerenciamento de SaaS. Quando você trabalha nos tutoriais, é incentivado a percorrer os scripts fornecidos para ver como os diferentes padrões de SaaS são implementados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

- O Azure PowerShell mais recente está instalado. Para obter detalhes, consulte [introdução ao Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Implantar o aplicativo Wingtip tickets

### <a name="plan-the-names"></a>Planejar os nomes

Nas etapas desta seção, você fornece um valor de *usuário* que é usado para garantir que os nomes de recursos sejam globalmente exclusivos e um nome para o *grupo de recursos* que contém todos os recursos criados por uma implantação do aplicativo. Para uma pessoa chamada *Ana alinen*, sugerimos:
- *Usuário:* **AF1**  *(suas iniciais, mais um dígito. Use um valor diferente (por exemplo, AF2) se você implantar o aplicativo uma segunda vez.)*
- *Grupo de recursos:* **Wingtip-MT-AF1** *(Wingtip-MT indica que este é o aplicativo multilocatário fragmentado. acrescentar o nome de usuário AF1 correlaciona o nome do grupo de recursos com os nomes dos recursos que ele contém.)*

Escolha seus nomes agora e anote-os. 

### <a name="steps"></a>Passos

1. Clique no botão azul **implantar no Azure** a seguir.
   - Ele abre o portal do Azure com o modelo de implantação Wingtip tickets SaaS.

     [![Botão para implantar no Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Insira os valores de parâmetro necessários para a implantação.

    > [!IMPORTANT]
    > Para esta demonstração, não use grupos de recursos, servidores ou pools preexistentes. Em vez disso, escolha **criar um novo grupo de recursos**. Elimine este grupo de recursos quando tiver terminado com a aplicação para parar a faturação relacionada.
    > Não use este aplicativo ou todos os recursos que ele cria, para produção. Alguns aspectos da autenticação e as configurações de firewall do servidor são intencionalmente inseguros no aplicativo para facilitar a demonstração.

    - Para **grupo de recursos** -selecione **criar novo**e forneça um **nome** para o grupo de recursos (diferencia maiúsculas de minúsculas).
        - Selecione um **local** na lista suspensa.
    - Para o **usuário** -recomendamos que você escolha um valor de **usuário** curto.

1. **Implemente a aplicação**.

    - Clique para concordar com os termos e condições.
    - Clique em **Comprar**.

1. Monitore o status da implantação clicando em **notificações**, que é o ícone de sino à direita da caixa de pesquisa. A implantação do aplicativo Wingtip leva aproximadamente cinco minutos.

   ![implementação concluída com êxito](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Baixar e desbloquear os scripts de gerenciamento

Enquanto o aplicativo estiver sendo implantado, baixe o código-fonte do aplicativo e os scripts de gerenciamento.

> [!NOTE]
> Conteúdos executáveis (scripts, DLLs) podem ser bloqueados pelo Windows quando arquivos zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, use as etapas a seguir para desbloquear o arquivo. zip antes de extrair. Ao desbloquear o arquivo. zip, você garante que os scripts tenham permissão para serem executados.

1. Navegue até [o repositório GitHub repositório wingtipticketssaas-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Clique em **clonar ou baixar**.
3. Clique em **baixar zip** e salve o arquivo.
4. Clique com o botão direito do mouse no arquivo **WingtipTicketsSaaS-MultiTenantDb-Master. zip** e selecione **Propriedades**.
5. Na guia **geral** , selecione **desbloquear**e clique em **aplicar**.
6. Clique em **OK**.
7. Extraia os arquivos.

Os scripts estão localizados na pasta *..\\repositório wingtipticketssaas-MultiTenantDb-master\\Learning modules\\* .

## <a name="update-the-configuration-file-for-this-deployment"></a>Atualizar o arquivo de configuração para esta implantação

Antes de executar qualquer script, defina o *grupo de recursos* e os valores de *usuário* em **userconfig. psm1**. Defina essas variáveis com os mesmos valores definidos durante a implantação.

1. Abra...\\módulos de aprendizado\\*userconfig. psm1* no *ISE do PowerShell*.
2. Atualize *ResourceGroupName* e *nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
3. Guarde as alterações.

Os valores definidos neste arquivo são usados por todos os scripts, portanto, é importante que eles sejam precisos. Se você reimplantar o aplicativo, deverá escolher valores diferentes para usuário e grupo de recursos. Em seguida, atualize o arquivo userconfig. psm1 novamente com os novos valores.

## <a name="run-the-application"></a>Executar a aplicação

No aplicativo Wingtip, os locatários são locais. Um local pode ser Concert Hall, um clube esportivo ou qualquer outro local que hospede eventos. Os locais se registram na Wingtip como clientes e um identificador de locatário é gerado para cada local. Cada local lista seus eventos futuros na Wingtip, portanto, o público pode comprar tíquetes para os eventos.

Cada local Obtém um aplicativo Web personalizado para listar seus eventos e vender tíquetes. Cada aplicativo Web é independente e isolado de outros locatários. Internamente, no banco de dados SQL do Azure, cada um deles é armazenado em um banco de dado multilocatário fragmentado, por padrão. Todos os dados são marcados com o identificador do locatário.

Uma página da Web **Hub de eventos** central fornece uma lista de links para os locatários em sua implantação específica. Use as etapas a seguir para experimentar a página da Web **Hub de eventos** e um aplicativo individual:

1. Abra o **Hub de eventos** em seu navegador da Web:
   - http://events.wingtip-mt.&lt; User&gt;. trafficmanager.net &nbsp; *(substitua &lt;usuário&gt; pelo valor de usuário da sua implantação.)*

     ![hub de eventos](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Clique em **Fabrikam Jazz Club** no **Hub de Eventos**.

   ![Eventos](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Traffic Manager do Azure

Para controlar a distribuição de solicitações de entrada, o aplicativo Wingtip usa o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md). A página de eventos para cada locatário inclui o nome do locatário em sua URL. Cada URL também inclui seu valor de usuário específico. Cada URL obedece ao formato mostrado usando as seguintes etapas:

- http://events.wingtip-mt.&lt; usuário&gt;. trafficmanager.net/*fabrikamjazzclub*

1. O aplicativo de eventos analisa o nome do locatário a partir da URL. O nome do locatário é *fabrikamjazzclub* na URL de exemplo anterior.
2. Em seguida, o aplicativo faz hash do nome do locatário para criar uma chave para acessar um catálogo usando o [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).
3. O aplicativo localiza a chave no catálogo e Obtém o local correspondente do banco de dados do locatário.
4. O aplicativo usa as informações de local para localizar e acessar o banco de dados que contém todos os dado para o locatário.

### <a name="events-hub"></a>Hub de eventos

1. O **Hub de eventos** lista todos os locatários que estão registrados no catálogo e seus locais.
2. O **Hub de eventos** usa metadados estendidos no catálogo para recuperar o nome do locatário associado a cada mapeamento para construir as URLs.

Em um ambiente de produção, você normalmente cria um registro DNS CNAME para [apontar um domínio de Internet da empresa](../traffic-manager/traffic-manager-point-internet-domain.md) para o perfil do Gerenciador de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que o aplicativo foi implantado, vamos colocá-lo em funcionamento! O script do PowerShell *demo-LoadGenerator* inicia uma carga de trabalho em execução para cada locatário. A carga do mundo real em muitos aplicativos SaaS é normalmente esporádica e imprevisível. Para simular esse tipo de carga, o gerador produz uma carga distribuída entre todos os locatários. A carga inclui intermitências aleatórias em cada locatário que ocorre em intervalos aleatórios. Leva vários minutos para que o padrão de carga surja, portanto, é melhor permitir que o gerador seja executado por pelo menos três ou quatro minutos antes de monitorar a carga.

1. No *ISE do PowerShell*, abra o...\\módulos de aprendizado\\utilitários\\script *demo-LoadGenerator. ps1* .
2. Prima **F5** para executar o script e iniciar o gerador de carga (deixe os valores predefinidos do parâmetro por agora).

O script *demo-LoadGenerator. ps1* abre outra sessão do PowerShell em que o gerador de carga é executado. O gerador de carga é executado nessa sessão como uma tarefa em primeiro plano que invoca trabalhos de geração de carga em segundo plano, um para cada locatário.

Depois que a tarefa em primeiro plano for iniciada, ela permanecerá em um estado de invocação de trabalho. A tarefa inicia trabalhos em segundo plano adicionais para quaisquer novos locatários que são provisionados subsequentemente.

Fechar a sessão do PowerShell interrompe todos os trabalhos.

Talvez você queira reiniciar a sessão do gerador de carga para usar valores de parâmetro diferentes. Nesse caso, feche a sessão de geração do PowerShell e, em seguida, execute novamente o *demo-LoadGenerator. ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Provisionar um novo locatário no banco de dados fragmentado

A implantação inicial inclui três locatários de exemplo no banco de dados *Tenants1* . Vamos criar outro locatário e observar seus efeitos no aplicativo implantado. Nesta etapa, você pressiona uma tecla para criar um novo locatário:

1. Abra...\\módulos de aprendizado\\provisionar e catalogar\\*provisiontenants. ps1* no *ISE do PowerShell*.
2. Pressione **F5** (não **F8**) para executar o script (Deixe os valores padrão por enquanto).

   > [!NOTE]
   > Você deve executar os scripts do PowerShell apenas pressionando a tecla **F5** , não pressionando **F8** para executar uma parte selecionada do script. O problema com **F8** é que a variável *$PSScriptRoot* não é avaliada. Essa variável é necessária para muitos scripts para navegar por pastas, invocar outros scripts ou importar módulos.

O novo locatário de corrida de bordo vermelho é adicionado ao banco de dados *Tenants1* e registrado no catálogo. O site de **eventos** de vendas do novo locatário é aberto no seu navegador:

![Novo inquilino](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Atualize o **Hub de eventos**e o novo locatário agora aparece na lista.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Provisionar um novo locatário em seu próprio banco de dados

O modelo multilocatário fragmentado permite que você escolha se deseja provisionar um novo locatário em um banco de dados que contenha outros locatários ou em um banco de dados próprio. Um locatário isolado em seu próprio banco de dados tem os seguintes benefícios:

- O desempenho do banco de dados do locatário pode ser gerenciado sem a necessidade de comprometer as necessidades de outros locatários.
- Se necessário, o banco de dados pode ser restaurado para um ponto anterior no tempo, porque nenhum outro locatário seria afetado.

Você pode optar por colocar clientes de avaliação gratuita ou clientes de economia em bancos de dados de vários locatários. Você pode colocar cada locatário Premium em seu próprio banco de dados dedicado. Se você criar muitos bancos de dados que contenham apenas um locatário, poderá gerenciá-los coletivamente em um pool elástico para otimizar os custos de recursos.

Em seguida, provisionmos outro locatário, desta vez em seu próprio banco de dados:

1. Em...\\módulos de aprendizado\\provisionar e catalogar\\*provisiontenants. ps1*, modifique *$TenantName* para **Salix salsa**, *$VenueType* para **dança** e *$Scenario* para **2**.

2. Pressione **F5** para executar o script novamente.
    - Essa tecla **F5** provisiona o novo locatário em um banco de dados separado. O banco de dados e o locatário são registrados no catálogo. Em seguida, o navegador é aberto na página eventos do locatário.

   ![Página de eventos do Salix salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Role até a parte inferior da página. Lá, na faixa, você vê o nome do banco de dados no qual o locatário está armazenado.

3. Atualizar o **Hub de eventos** e os dois novos locatários agora aparecem na lista.

## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bancos de dados de locatário

Agora, vamos examinar alguns dos recursos que foram implantados:

1. Na [portal do Azure](https://portal.azure.com), navegue até a lista de grupos de recursos. Abra o grupo de recursos que você criou quando implantou o aplicativo.

   ![grupo de recursos](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Clique em **Catalog-mt&lt;** servidor de&gt;de usuário. O servidor de catálogo contém dois bancos de dados chamados *tenantcatalog* e *basetenantdb*. O banco de dados *basetenantdb* é um banco de dados de modelo vazio. Ele é copiado para criar um novo banco de dados de locatário, seja usado para muitos locatários ou apenas um locatário.

   ![servidor de catálogo](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Volte para o grupo de recursos e selecione o servidor *tenants1-MT* que contém os bancos de dados de locatário.
    - O banco de dados tenants1 é um banco de dados multilocatário no qual os três locatários originais, mais o primeiro locatário adicionado, são armazenados. Ele é configurado como um banco de dados padrão de DTU 50.
    - O banco de dados **salixsalsa** mantém o local Salix salsa dança como seu único locatário. Ele é configurado como um banco de dados Standard Edition com 50 DTUs por padrão.

   ![servidor de locatários](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Monitorar o desempenho do banco de dados

Se o gerador de carga estiver em execução por vários minutos, a telemetria suficiente estará disponível para examinar os recursos de monitoramento de banco de dados incorporados ao portal do Azure.

1. Navegue até o servidor de **&gt;de usuário do tenants1-mt&lt;** e clique em **tenants1** para exibir a utilização de recursos para o banco de dados que tem quatro locatários nele. Cada locatário está sujeito a uma carga pesada esporádica do gerador de carga:

   ![monitorar tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   O gráfico de utilização de DTU ilustra bem como um banco de dados multilocatário pode dar suporte a uma carga de trabalho imprevisível em vários locatários. Nesse caso, o gerador de carga está aplicando uma carga esporádica de aproximadamente 30 DTUs a cada locatário. Essa carga equivale à utilização de 60% de um banco de dados de DTU 50. Os picos que excedem 60% são o resultado da aplicação da carga em mais de um locatário ao mesmo tempo.

2. Navegue até o servidor de **&gt;de usuário do tenants1-mt&lt;** e clique no banco de dados **salixsalsa** . Você pode ver a utilização de recursos nesse banco de dados que contém apenas um locatário.

   ![banco de dados salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

O gerador de carga está aplicando uma carga semelhante a cada locatário, independentemente do banco de dados em que cada locatário está. Com apenas um locatário no banco de dados **salixsalsa** , você pode ver que o banco de dados pode sustentar uma carga muito maior do que o banco de dados com vários locatários. 

### <a name="resource-allocations-vary-by-workload"></a>As alocações de recursos variam por carga de trabalho

Às vezes, um banco de dados multilocatário requer mais recursos para um bom desempenho do que um banco de dados de locatário único, mas nem sempre. A alocação ideal de recursos depende das características de carga de trabalho específicas para os locatários no seu sistema.

As cargas de trabalho geradas pelo script do gerador de carga são apenas para fins ilustrativos.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicativos SaaS multilocatários, consulte [padrões de design para aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md).

- Para saber mais sobre pools elásticos, consulte:

  - [Os pools elásticos ajudam você a gerenciar e dimensionar vários bancos de dados SQL do Azure](sql-database-elastic-pool.md)
  - [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> - Como implantar o aplicativo de banco de dados multilocatário do Wingtip tickets SaaS.
> - Sobre os servidores e os bancos de dados que compõem o aplicativo.
> - Os locatários são mapeados para seus dados com o *Catálogo*.
> - Como provisionar novos locatários em um banco de dados multilocatário e banco de dados de locatário único.
> - Como exibir a utilização do pool para monitorar a atividade do locatário.
> - Como excluir recursos de exemplo para interromper a cobrança relacionada.

Agora experimente o [tutorial provisionar e catalogar](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Botão para implantar no Azure."

