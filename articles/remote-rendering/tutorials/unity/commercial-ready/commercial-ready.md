---
title: Criar uma aplicação do Azure Remote Rendering pronta para comercialização
description: Estratégias e considerações para a criação de uma aplicação pronta comercial utilizando a renderização remota Azure
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 0ff6d064b6bfabbd8a648c23b5648aae9b12a11d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207500"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Tutorial: Criação de uma aplicação de renderização remota Azure

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Gestão de sessão para aplicações comerciais
> * Sessões de rastreio para faturação
> * Otimizar a experiência do utilizador em torno do tempo de carregamento da sessão
> * Considerações em torno da latência da rede

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial baseia-se no [Tutorial: Assegurar a renderização remota do Azure e o armazenamento de modelos.](../security/security.md)

## <a name="introduction-to-commercial-readiness"></a>Introdução à Prontidão Comercial

A renderização remota Azure expande o que é possível com a realidade mista. Uma vez que os fundamentos são integrados na sua solução, existem uma série de considerações adicionais para garantir que a sua solução é segura, escalável e pronta para entregar valor.

Este módulo apresenta-lhe algumas capacidades adicionais que poderá ter de considerar para a sua aplicação comercial.

Para uma visão geral das melhores práticas de arquitetura em todo o sistema, visite:

* [Centro de Arquitetura do Azure](/azure/architecture/)
* [Iniciar Guia para Desenvolvedores Azure](../../../../guides/developer/azure-developer-guide.md)

## <a name="analytics"></a>Análise

A integração de ferramentas de análise pode ajudar a gerir, rastrear e melhorar a sua solução.

Para obter uma lista completa dos recursos de análise disponíveis para si, visite:

* [Serviços de Análise do Azure](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>Rastreio de utilização para faturação

Acompanhar o consumo de Renderização Remota Azure por várias equipas internas ou clientes externos torna-se uma consideração importante, especialmente em situações de multi-inquilinos.

Para isso, a Azure oferece um serviço chamado marcação de recursos, que associa o consumo do serviço de renderização remota Azure a cada cliente.

Para obter mais informações sobre o nome de recursos e a marcação, um bom lugar para começar é:

* [Guia de decisão de nomeação e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%252fazure%252fazure-resource-manager%252fmanagement%252ftoc.json)

### <a name="diagnostics"></a>Diagnóstico

Ferramentas poderosas como o Event Tracing for Windows (ETW) e o Event Trace Logging (ETL) facilitam a geração de eventos de rastreio dentro da sua aplicação e podem ajudar a diagnosticar rede, ingestão de conteúdos, sessão, aplicação e outros problemas que possam surgir numa implementação de solução comercial.

Para mais informações, visite:

* [Criar traços de desempenho do lado do cliente](../../../how-tos/performance-tracing.md)
* [Como recolher o rastreio de eventos para os dados do Windows (ETW)](/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Utilização do Portal do Dispositivo Windows: Registar registo](/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Análise de utilização

O Azure Application Insights ajuda-o a compreender como as pessoas usam a sua aplicação de renderização remota Azure. Sempre que atualizar a sua aplicação, pode avaliar o seu bom funcionamento para os utilizadores e melhorar a sua solução em conformidade. Com este conhecimento, pode tomar decisões baseadas em dados sobre os seus próximos ciclos de desenvolvimento.

Para mais informações, visite:

* [Análise de utilização com insights de aplicações](../../../../azure-monitor/app/usage-overview.md)

## <a name="fast-startup-time-strategies"></a>Estratégias rápidas de tempo de arranque

A sua caixa de utilização pode necessitar de um arranque rápido do lançamento da aplicação para a visualização do modelo 3D. Por exemplo, durante uma reunião importante onde é fundamental ter tudo a funcionar antes do tempo. Outro exemplo é durante uma revisão do modelo CAD 3D onde a iteração rápida de design entre uma aplicação CAD e realidade mista é fundamental para a eficiência.

A renderização remota Azure requer modelos 3D pré-processados, e a Azure demora atualmente vários minutos a criar uma sessão e a carregar um modelo para renderização. Tornar este processo o mais perfeito e rápido possível requer a preparação dos dados do modelo 3D e da sessão ARR antes do tempo.

As sugestões partilhadas aqui não fazem parte da renderização remota padrão do Azure, mas pode implementá-las por conta própria para tempos de arranque mais rápidos.

### <a name="initiate-early"></a>Iniciar cedo

Para reduzir o tempo de arranque, a solução mais simples é mover a criação e a inicialização da sessão o mais cedo possível no fluxo de trabalho do utilizador. Uma estratégia é inicializar a sessão assim que se souber que será necessária uma sessão de ARR. Isto será muitas vezes quando o utilizador começa a carregar um modelo 3D para o Azure Blob Storage para usar com renderização remota Azure. Neste caso, a criação e a inicialização da sessão podem ser iniciadas ao mesmo tempo que o modelo 3D carrega de forma a que ambos os fluxos de trabalho funcionem em paralelo.

Este processo pode ser simplificado ainda mais garantindo que os recipientes de entrada e saída escolhidos do Azure Blob estão no mesmo centro de dados regional que a sessão de renderização remota Azure.

### <a name="scheduling"></a>Agendamento

Se sabe que tem uma necessidade futura de renderização remota do Azure, pode agendar uma data e hora específicas para iniciar a sessão de renderização remota Azure.

Esta opção poderia ser oferecida através de um portal web onde as pessoas podem tanto carregar um modelo 3D como agendar uma hora para vê-lo no futuro. Este seria também um bom lugar para pedir outras preferências, como a renderização [*Standard*](../../../reference/vm-sizes.md) ou [*Premium.*](../../../reference/vm-sizes.md) *A* renderização premium pode ser adequada se houver vontade de mostrar uma mistura de ativos onde o tamanho ideal é mais difícil de determinar automaticamente ou a necessidade de garantir que a região de Azure tem VMs disponíveis nesse momento especificado.

### <a name="session-pooling"></a>Pooling de sessão

Nas situações mais exigentes, outra opção é o pooling de sessões, onde uma ou mais sessões são criadas e inicializadas a todo o momento. Isto cria um pool de sessão para uso imediato por um utilizador que solicita. O lado negativo desta abordagem é que uma vez iniciada a VM, a faturação para o serviço começa. Pode não ser rentável manter sempre um pool de sessões em funcionamento, mas com base em análises, pode ser possível prever cargas máximas ou pode ser combinado com a estratégia de agendamento acima para prever quando serão necessárias sessões e acelerar para cima e para baixo no pool da sessão em conformidade.

Esta estratégia também ajuda a otimizar a escolha entre as sessões *Standard* e *Premium* de uma forma mais dinâmica, pois seria muito mais rápido alternar entre os dois tipos numa única sessão de utilizador, como o caso em que um modelo de complexidade *Premium* é visto primeiro, seguido por um que pode funcionar dentro da *Standard.* Se estas sessões de utilizador forem bastante longas, pode haver poupanças significativas de custos.

Para mais informações sobre as sessões de renderização remota do Azure, confira:

* [Sessões do Remote Rendering](../../../concepts/sessions.md)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Estratégias de encaminhamento de tamanho do servidor standard vs. Premium

A necessidade de selecionar se criar um tamanho de servidor *Standard* ou *Premium* apresenta um desafio na conceção da sua experiência de utilizador e do sistema de ponta a ponta. Embora usar apenas sessões *Premium* seja uma opção, as sessões *Standard* usam muito menos recursos de computação Azure e são mais baratas que *Premium.* Isto proporciona uma forte motivação para usar sessões *Standard* sempre que possível e apenas usar *Premium* quando necessário.

Aqui partilhamos várias opções, do menos ao mais abrangente, para abordar o desejo de gerir escolhas de sessão.

### <a name="use-only-standard-or-premium"></a>Utilize apenas standard ou Premium

Se tem a certeza de que as suas necessidades ficarão *sempre* abaixo do limiar entre *a Standard* e *a Premium,* então isto simplifica consideravelmente a sua decisão. Basta usar *o Standard.* Tenha em mente, no entanto, que o impacto na experiência do utilizador é significativo se a complexidade total dos ativos carregados for rejeitada como demasiado complexa para uma sessão *Standard.*

Da mesma forma, se espera que uma grande parte das utilizações exceda o limiar entre *Standard* e *Premium*, ou o custo não seja um fator chave no seu caso de utilização, então escolher sempre *o Premium* também é uma opção para mantê-lo simples.

### <a name="ask-the-user"></a>Pergunte ao utilizador

Se deseja suportar tanto o *Standard* como o *Premium,* a forma mais fácil de determinar que tipo de sessão a instantanear é perguntar ao utilizador quando seleciona ativos 3D para visualizar. O desafio com esta abordagem é que requer que o utilizador compreenda a complexidade do ativo 3D ou mesmo vários ativos que serão vistos. Normalmente, isto não é recomendado por essa razão. Se o utilizador selecionar errado e escolher *o Standard,* a experiência do utilizador resultante pode ser comprometida num momento inoportuno.

### <a name="analyze-the-3d-model"></a>Analisar o modelo 3D

Outra abordagem relativamente simples é analisar a complexidade dos ativos 3D selecionados. Se a complexidade do modelo estiver abaixo do limiar para a *Standard,* inicie uma sessão *Standard,* caso contrário inicie uma sessão *Premium.* Aqui, o desafio é que uma única sessão pode finalmente ser usada para visualizar vários modelos dos quais alguns podem exceder o limiar de complexidade de uma sessão *Standard,* resultando na incapacidade de usar perfeitamente a mesma sessão para uma sequência de diferentes ativos 3D.

### <a name="automatic-switching"></a>Comutação automática

A comutação automática entre as sessões *Standard* e *Premium* pode fazer muito sentido num design de sistema que também inclui o pooling de sessão. Esta estratégia permite uma maior otimização da utilização dos recursos. À medida que o utilizador carrega os modelos para visualização, a complexidade é determinada e o tamanho correto da sessão é solicitado a partir do serviço de pooling de sessão.

## <a name="working-with-networks"></a>Trabalhar com redes

### <a name="diagnostics"></a>Diagnóstico

A renderização remota Azure requer uma ligação rápida à internet com baixa latência. A qualidade da rede do utilizador pode ter um impacto significativo na qualidade da experiência. Dado que os seus clientes são suscetíveis de ter configurações de rede diferentes e apenas ocasionalmente de latência de rede, as ferramentas de diagnóstico são fundamentais.  

Para garantir que pode oferecer uma experiência de qualidade consistentemente elevada, recomendamos que integre ferramentas de análise do lado do servidor e do lado do cliente nas suas aplicações de renderização remota Azure. Ao fazê-lo, arma-o com a informação de que necessitará para diagnosticar e mitigar quaisquer problemas de rede que os seus clientes possam estar a passar.

### <a name="client-network-configurations"></a>Configurações de rede de clientes

Um dos maiores desafios no desenvolvimento de soluções de colaboração robustas que são implantadas em uma grande variedade de ambientes empresariais está a ser preparada para as diferentes configurações de topologia de rede e firewall empresarial que os seus clientes podem usar.

Muitas empresas bloqueiam todo o tráfego entre pares dentro de uma LAN. Isto dificulta tirar partido da simplicidade e ux simplificado da descoberta automática lan para estabelecer uma sessão partilhada local entre todos os casos descobertos da sua aplicação de realidade mista.

Outros pontos de falha potenciais são routers configurados para acelerar intencionalmente a largura de banda e firewalls que bloqueiam a maioria das portas TCP/IP.

Sempre que estiver a planear utilizar a renderização remota do Azure numa rede desconhecida, recomendamos o seguinte:

* Forneça uma lista de verificação prévia para avaliar a prontidão da rede.
* Certifique-se de que o centro de dados regional apropriado pode servir o pedido.
* Dê muito tempo para diagnosticar qualquer problema.
* Traga um hotspot móvel com um plano de dados de alta largura de banda como cópia de segurança.

### <a name="end-to-end-bandwidth"></a>Largura de banda de ponta a ponta

É importante avaliar as capacidades de largura de banda para cada perna da rede que pode existir entre o VM de Renderização Remota Azure e o cliente final. Tenha em mente que o segmento de rede do centro de dados Azure para o ISP do cliente pode ser mais um fator limitativo do que do ISP para o cliente. O Teste de Velocidade de Download Blob pode ser usado para ajudar a diagnosticar tais problemas.

### <a name="bandwidth-competition"></a>Competição de largura de banda

Ao desenhar a sua aplicação de realidade mista, lembre-se que diferentes funcionalidades da app podem competir com a Renderização Remota Azure para largura de banda. O exemplo mais provável é quando muitos participantes numa única sala esperam usar simultaneamente o ARR para visualizar um ativo 3D. Cada parte do fluxo de dados da rede terá de ter capacidade para transportar a soma total de todos os fluxos de dados ARR combinados.

Outros exemplos incluem vídeo sonoro, uploads de fundo simultâneos de outros conteúdos relacionados, e chat de voz, particularmente onde há muitos participantes e o sistema está usando uma abordagem distribuída peer-to-peer em oposição a um servidor de mistura de áudio na abordagem intermédia.

Para mais informações sobre análise de rede, consulte:

* [Teste de velocidade de download de blob de armazenamento Azure](https://www.azurespeed.com/Azure/Download)
* [Estatísticas de latência de ida e volta da rede Azure](../../../../networking/azure-network-latency.md)
* [Traços de desempenho do lado do servidor](../../../overview/features/performance-queries.md)
* [Traços de desempenho do lado do cliente](../../../how-tos/performance-tracing.md)

## <a name="collaboration-considerations"></a>Considerações de colaboração

Alguns dos usos mais valiosos da Renderização Remota Azure envolvem colaboração entre vários participantes que vêem a mesma experiência 3D ao mesmo tempo. Nestas sessões partilhadas, é importante reconhecer que cada participante necessitará de uma sessão única de renderização remota Azure, independentemente de estarem ou não localizados no mesmo local na mesma rede.

Isto é verdade porque cada participante está realmente vendo a mesma experiência de diferentes pontos de vista, o que requer que os mesmos ativos 3D sejam renderizados de cada uma dessas perspetivas simultaneamente.

### <a name="multiple-azure-remote-rendering-sessions"></a>Múltiplas sessões de renderização remotas Azure

Se pretende apoiar experiências partilhadas com a Renderização Remota Azure, os sistemas que criou para criar e gerir sessões ARR terão de estar preparados para iniciar várias sessões. Estas sessões podem ter de ser inicializadas em diferentes centros de dados Azure se os participantes estiverem geograficamente dispersos.

O seu sistema também deve gerir a possibilidade de que um ou mais dos participantes possam estar numa região geográfica que atualmente não é suportada pela Azure Remote Rendering ou que atualmente não tenha instâncias VM de renderização remota Azure disponíveis.

Esta gestão de múltiplas sessões simultâneas pode ser simplificada quando combinada com o pooling de sessão e outras estratégias discutidas neste documento.

### <a name="azure-blob-storage-considerations"></a>Considerações de armazenamento Azure Blob

Todas as sessões de ARR simultâneas podem fazer referência ao mesmo SAS URI para que o modelo convertido seja visualizado. Isto permite carregar e converter os ativos 3D desejados uma vez e, em seguida, partilhá-lo em todas as sessões. Isto é especialmente verdade quando os participantes são co-localizados e usando o mesmo centro de dados onde não há preocupações de desempenho relacionadas com o Azure Blob Storage localizado num centro de dados diferente do servidor de renderização remota Azure e do utilizador.

Se os ativos 3D forem normalmente carregados para uma única sessão de visualização e depois descartados, como numa sessão de revisão de design, a região geográfica do Azure Blob Storage em relação ao servidor de renderização remota Azure também é menos crítica.

No entanto, para ativos 3D que serão usados repetidamente, como num caso de uso de formação, recomendamos manter os ativos 3D prontos a fazer em armazenamento de bolhas em cada centro de dados regional onde planeia utilizar a Renderização Remota Azure. Isto pode ser automatizado usando a redundância de armazenamento Azure. O CDN também é frequentemente utilizado para este fim, mas esta ainda não é uma opção para renderização remota Azure.

Para obter mais informações:

* [Experiências partilhadas em Realidade Mista](/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Redundância de armazenamento Azure](../../../../storage/common/storage-redundancy.md)

## <a name="managing-model-access"></a>Gestão do acesso a modelos

A utilização da renderização remota Azure requer uma cuidadosa consideração da infraestrutura de ponta a ponta para a gestão dos modelos 3D.

Uma vantagem da utilização da Renderização Remota Azure é que os grandes ativos 3D nunca precisam de ser transmitidos diretamente ao dispositivo de realidade mista antes de serem vistos.  Além disso, uma vez que um ativo 3D tenha sido carregado e convertido para uso com renderização remota Azure, qualquer número de utilizadores pode partilhar essa única instância do modelo 3D.

### <a name="considerations-for-3d-model-access"></a>Considerações para acesso a modelo 3D

Aqui ficam algumas considerações fundamentais ao decidir sobre a sua estratégia de acesso ao modelo.

Com base no caso de utilização previsto, determine o melhor local ou combinação de locais para permitir que um utilizador selecione os ativos 3D para visualização. Algumas opções comuns são:

* Diretamente dentro da experiência de realidade mista
* Através de um portal web de acompanhantes
* Em um ambiente de trabalho de acompanhante ou aplicação móvel

Se o seu caso de utilização tiver padrões de utilização onde o mesmo ativo 3D pode ser carregado várias vezes, o back-end irá rastrear quais os modelos que já estão convertidos para uso com ARR de modo a que um modelo seja apenas pré-processado uma vez para várias seleções futuras. Um exemplo de revisão de design seria quando uma equipa tem acesso a um ativo 3D original comum. Espera-se que cada membro da equipa reveja o modelo utilizando o ARR em algum momento do seu fluxo de trabalho. Só a primeira vista desencadearia o passo de pré-processamento. As opiniões subsequentes procurariam o ficheiro pós-processado associado no recipiente de saída SAS.

Dependendo do caso de utilização, provavelmente quererá determinar e potencialmente persistir o tamanho correto do servidor de renderização remota Azure, *Standard* ou *Premium*, para cada ativo 3D ou grupo de ativos que serão vistos juntos na mesma sessão.  

### <a name="on-device-model-selection-list"></a>Lista de seleção de modelos no dispositivo

Em muitos casos de uso, como uma formação, orientação de tarefas ou aplicação de marketing, o conjunto de ativos 3D a ser comumente visto na Renderização Remota Azure pode ser bastante estático. Nestas situações, um conjunto curado de ativos 3D pode ser pré-convertido e disponibilizado através de uma base de dados que contenha as informações necessárias para preencher uma lista de seleção de ativos curados. Estes dados podem então ser recuperados a partir da aplicação de realidade mista para preencher um menu de seleção.

Isto pode ser dado um passo mais além, oferecendo também uma forma de carregar ativos 3D privados, exclusivos de cada indivíduo ou grupo. Essa lista de ativos privados poderia então ser combinada com a lista de ativos comuns e curados na experiência do utilizador para escolher ativos 3D para visualizar.

### <a name="on-device-onedrive-access"></a>Acesso OneDrive no dispositivo

Dado que um picker de ficheiro OneDrive é incorporado de forma nativa nos dispositivos de realidade mista da Microsoft, selecionar ativos 3D no dispositivo a partir do OneDrive é apelativo, especialmente para casos de utilização em casos em que é comum carregar modelos 3D diferentes ou modificados. Neste cenário, o utilizador selecionaria um ou mais ativos 3D através do picker de ficheiro OneDrive dentro da sua aplicação de realidade mista. Os ativos 3D seriam então migrados para um recipiente de entrada SAS, convertido num recipiente de saída SAS, e anexados à sessão ARR. Idealmente, a aplicação de realidade mista invocaria um processo baseado na nuvem para executar estes passos em vez de mover todos os bits do OneDrive para o dispositivo e de volta para o Azure Blob Storage.

Esta abordagem poderia ser dada um passo mais além, persistindo uma associação entre ativos 3D que foram previamente vistos de modo a que, quando o mesmo modelo for novamente escolhido a partir do OneDrive, a aplicação pode contornar o processo de conversão e carregar diretamente o ativo 3D convertido associado através do seu SAS URI.

Para obter mais informações:

* [Modelo de automatização de energia da Microsoft para a replicação do armazenamento OneDrive para Azure](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [Visão geral da API de armazenamento de ficheiros OneDrive](/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Acesso direto ao CAD

Um caso de uso convincente para a realidade mista é as revisões de design do trabalho do CAD em progresso. Neste cenário, o tempo de carga mais rápido do ambiente de trabalho para a realidade mista é fundamental. Uma solução ideal poderia envolver o desenvolvimento de plugins para aplicações CAD específicas. Estes plugins geririam diretamente todos os aspetos do processo de carga, conversão e visualização:

* Fornecer um UX para:
  * Emparelhar a aplicação CAD com um dispositivo de realidade mista específico (uma vez).
  * Solicite que a geometria selecionada seja vista nesse dispositivo de realidade mista.
* Se ainda não estiver em execução, rode a sessão de renderização remota Azure para que possa processar em paralelo enquanto faz o upload e a conversão do ficheiro CAD
* Normalizar os dados de geometria cad para um dos formatos suportados pela Renderização Remota Azure
* Transmita os dados normalizados diretamente para o recipiente de entrada de armazenamento Azure Blob
* Iniciar o processo de conversão do modelo
* Ligue o recipiente de saída do modelo SAS URI à sessão de renderização remota Azure
* Notifique a aplicação de realidade mista emparelhada de que o modelo está disponível e pronto para visualização e forneça o recipiente de saída SAS URI para que a aplicação possa anexá-lo à sessão.

Uma abordagem muito mais simples, mas ligeiramente menos simplificada, poderia automatizar o processo de poupança do modelo 3D para um disco rígido local e, em seguida, iniciar um processo para transmitir esse ficheiro guardado para o recipiente de entrada SAS.

### <a name="azure-marketplace"></a>Azure Marketplace

Muitos clientes empresariais determinam que o seu Azure Stack pode ser implantado sob as suas próprias contas Ezure e credenciais por razões de segurança. Para tornar isto possível, irá considerar a embalagem da sua aplicação gerida Azure de modo a que possa ser publicada no Azure Marketplace como uma Oferta de Aplicação Azure.

Para obter mais informações:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Tutorial: Publicar aplicações geridas pela Azure no Mercado](../../../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)

### <a name="security"></a>Segurança

É fundamental construir a sua solução de renderização remota Azure de ponta a ponta para a segurança do zero. Existem muitos aspetos de segurança a considerar no desenho da sua solução de ponta a ponta, incluindo:

* Estratégias de autenticação
* Gestão de acessos - grupos, políticas e permissões
* Arquitetura multi-inquilino
* Encriptação de armazenamento e transferência de dados
* Fichas de utilização temporária
* Ataques de negação de serviço distribuídos (DDoS)
* Deteção de ameaças
* VPNs e redes seguras
* Firewalls
* Certificação e gestão secreta de chaves
* Vulnerabilidade e explorações de aplicações

Para a autenticação, é aconselhável mover o máximo possível da autenticação e gestão de sessão do ARR para um Serviço Web Azure. Isto resultará numa solução mais bem gerida e mais segura.

Para obter mais informações:

* [Autenticação do serviço Azure Ad](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-service-authentication)
* [Fortaleça a sua postura de segurança com o Azure](https://azure.microsoft.com/overview/security/)
* [Segurança na Nuvem](https://azure.microsoft.com/product-categories/security/)