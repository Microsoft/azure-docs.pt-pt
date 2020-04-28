---
title: Fatores a influenciar o desempenho do Azure AD Connect
description: Este documento explica como vários fatores influenciam o motor de provisionamento Azure AD Connect. Estes fatores ajudarão as organizações a planear a sua implementação azure AD Connect para garantir que cumpre os seus requisitos de sincronização.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5518d516848ba7c006827faa41ff76bbca35d0c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76897050"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Fatores a influenciar o desempenho do Azure AD Connect

O Azure AD Connect sincroniza o seu Diretório Ativo a Azure AD. Este servidor é um componente crítico de mover as identidades do utilizador para a nuvem. Os fatores primários que afetam o desempenho de um Azure AD Connect são:

| **Fator de design**| **Definição** |
|:-|-|
| Topologia| A distribuição dos pontos finais e componentes o Azure AD Connect deve ser gerida na rede. |
| Escala| O número de objetos como os utilizadores, grupos e OUs, a ser gerido pelo Azure AD Connect. |
| Hardware| O hardware (físico ou virtual) para o Azure AD Connect e a capacidade de desempenho dependente de cada componente de hardware, incluindo CPU, memória, rede e configuração de disco rígido. |
| Configuração| Como o Azure AD Connect processa os diretórios e a informação. |
| Carregar| A frequência das alterações do objeto. As cargas podem variar durante uma hora, dia ou semana. Dependendo do componente, poderá ter de conceber para carga máxima ou carga média. |

O objetivo deste documento é descrever os fatores que influenciam o desempenho do motor de provisionamento Azure AD Connect. Organizações grandes ou complexas (organizações que aprovisionam mais de 100.000 objetos) podem usar as recomendações para otimizar a sua implementação do Azure AD Connect, caso experimentem quaisquer problemas de desempenho aqui descritos. Os outros componentes da Azure AD Connect, como a saúde e os agentes da [Azure AD Connect](how-to-connect-health-agent-install.md) não estão abrangidos aqui.

> [!IMPORTANT]
> A Microsoft não suporta modificar ou operar o Azure AD Connect fora das ações que estão formalmente documentadas. Qualquer uma destas ações pode resultar num estado inconsistente ou não apoiado de sincronização azure AD Connect. Como resultado, a Microsoft não pode fornecer suporte técnico para tais implementações.

## <a name="azure-ad-connect-component-factors"></a>Fatores componentes de ligação ad azure

O diagrama seguinte mostra uma arquitetura de alto nível de motor de provisionamento que liga a uma única floresta, embora várias florestas sejam apoiadas. Esta arquitetura mostra como os vários componentes interagem entre si.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

O motor de provisionamento liga-se a cada floresta de Diretório Ativo e à Azure AD. O processo de leitura da informação de cada diretório chama-se Import. A exportação refere-se à atualização dos diretórios do motor de provisionamento. Sync avalia as regras de como os objetos vão fluir dentro do motor de provisionamento. Para um mergulho mais profundo, pode consultar o [Sincron de Ligação AD Azur: Compreender a arquitetura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

O Azure AD Connect utiliza as seguintes áreas de encenação, regras e processos para permitir a sincronização do Ative Directory para a AD Azure:

* **Connector Space (CS)** - Os objetos de cada diretório conectado (CD), os diretórios reais, são encenados aqui primeiro antes de poderem ser processados pelo motor de provisionamento. A Azure AD tem o seu próprio CS e cada floresta a que se conecta tem o seu próprio CS.
* **Metaverso (MV)** - Os objetos que precisam de ser sincronizados são criados aqui com base nas regras de sincronização. Os objetos devem existir no MV antes de poderem povoar objetos e atributos aos outros diretórios conectados. Só há um MV.
* Regras de **sincronização** - Decidem quais os objetos que serão criados (projetados) ou ligados (unidos) a objetos no MV. As regras de sincronização também decidem quais os valores atributos que serão copiados ou transformados de e para os diretórios.
* Executar perfis - Agrega os passos de processo de cópia de objetos e os seus valores de atributo de acordo com as regras de sincronização entre as **áreas** de encenação e os diretórios conectados.

Existem diferentes perfis de execução para otimizar o desempenho do motor de provisionamento. A maioria das organizações usará os horários padrão e executará perfis para operações normais, mas algumas organizações podem ter que [alterar o horário](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) ou desencadear outros perfis de execução para atender a situações incomuns. Estão disponíveis os seguintes perfis de execução:

### <a name="initial-sync-profile"></a>Perfil de sincronização inicial

O perfil de sincronização inicial é o processo de leitura dos diretórios conectados, como uma floresta de Diretório Ativo, pela primeira vez. Em seguida, faz uma análise de todas as entradas na base de dados do motor sincronizado. O ciclo inicial criará novos objetos em Azure AD e levará tempo extra para completar se as suas florestas de Diretório Ativo forem grandes. A sincronização inicial inclui os seguintes passos:

1. Importação total de todos os conectores
2. Sincronização completa em todos os conectores
3. Exportação de todos os conectores

### <a name="delta-sync-profile"></a>Perfil de sincronização delta

Para otimizar o processo de sincronização, este perfil de execução processa apenas as alterações (cria, elimina e atualiza) dos objetos nos seus diretórios conectados, desde o último processo de sincronização. Por padrão, o perfil de sincronização delta funciona a cada 30 minutos. As organizações devem esforçar-se para manter o tempo que leva para menos de 30 minutos, para garantir que o Azure AD está atualizado. Para monitorizar a saúde do Azure AD Connect, utilize o agente de monitorização da [saúde](how-to-connect-health-sync.md) para ver quaisquer problemas com o processo. O perfil de sincronização delta inclui os seguintes passos:

1. Importação delta em todos os conectores
2. Sincronização delta em todos os conectores
3. Exportação de todos os conectores

Um cenário típico de sincronização da organização empresarial delta é:

- ~1% dos objetos são eliminados
- ~1% dos objetos são criados
- ~5% dos objetos são modificados

A sua taxa de variação pode variar dependendo da frequência com que a sua organização atualiza os utilizadores no seu Diretório Ativo. Por exemplo, taxas de mudança mais elevadas podem ocorrer com a sazonalidade da contratação e redução da força de trabalho.

### <a name="full-sync-profile"></a>Perfil de sincronização completo

É necessário um ciclo de sincronização completo se tiver efeito alguma das seguintes alterações de configuração:



- Aumentou o âmbito dos objetos ou atributos a importar dos diretórios conectados. Por exemplo, quando adiciona um domínio ou U ao seu âmbito de importação.
- Fez alterações nas regras de sincronização. Por exemplo, quando se cria uma nova regra para preencher o título de um utilizador em Azure AD a partir de extension_attribute3 em Diretório Ativo. Esta atualização requer que o motor de provisionamento reexamine todos os utilizadores existentes para atualizar os seus títulos para aplicar a alteração que vai para a frente.

As seguintes operações estão incluídas num ciclo de sincronização completo:

1. Importação total de todos os conectores
2. Sincronização full/Delta em todos os conectores
3. Exportação de todos os conectores

> [!NOTE]
> É necessário um planeamento cuidadoso ao fazer atualizações a granel para muitos objetos no seu Ative Directory ou Azure AD. As atualizações a granel farão com que o processo de sincronização delta deleve mais tempo na importação, uma vez que muitos objetos mudaram. As importações longas podem acontecer mesmo que a atualização a granel não influencie o processo de sincronização. Por exemplo, a atribuição de licenças a muitos utilizadores em Azure AD causará um longo ciclo de importação a partir de Azure AD, mas não resultará em alterações de atributos no Ative Directory.

### <a name="synchronization"></a>Sincronização

O tempo de execução do processo de sincronização tem as seguintes características de desempenho:

* O Sync é roscado único, o que significa que o motor de provisionamento não faz qualquer processamento paralelo de perfis de execução de diretórios, objetos ou atributos conectados.
* O tempo de importação cresce linearmente com o número de objetos a ser sincronizados. Por exemplo, se 10.000 objetos demorarem 10 minutos a importar, então 20.000 objetos demorarão aproximadamente 20 minutos no mesmo servidor.
* A exportação também é linear.
* A sincronização crescerá exponencialmente com base no número de objetos com referências a outros objetos. Os membros do grupo e os grupos aninhados têm o principal impacto de desempenho, porque os seus membros se referem a objetos de utilizador ou outros grupos. Estas referências devem ser encontradas e referenciadas a objetos reais no MV para completar o ciclo de sincronização.

### <a name="filtering"></a>Filtragem

A dimensão da topologia do Diretório Ativo que pretende importar é o fator número um que influencia o desempenho e o tempo global que os componentes internos do motor de provisionamento demorarão.

[A filtragem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) deve ser utilizada para reduzir os objetos aos objetos sincronizados. Evitará que objetos desnecessários sejam processados e exportados para a AD Azure. Por ordem de preferência, estão disponíveis as seguintes técnicas de filtragem:



- **Filtragem baseada em domínios** - utilize esta opção para selecionar domínios específicos para sincronizar com o Azure AD. Tem de adicionar e remover domínios da configuração do motor de sincronização quando fizer alterações na sua infraestrutura no local depois de instalar o sincronde Azure AD Connect.
- **Filtragem da Unidade de Organização (OU)** - utiliza As EUs para direcionar objetos específicos em domínios de Diretório Ativo para o fornecimento à AD Azure. A filtragem ou é o segundo mecanismo de filtragem recomendado, porque utiliza consultas simples de âmbito LDAP para importar um subconjunto menor de objetos do Ative Directory.
- **Filtração de atributos por objeto** - utiliza os valores do atributo em objetos para decidir se o objeto específico no Diretório Ativo é provisionado em Azure AD. A filtragem do atributo é ótima para afinar os filtros, quando a filtragem de domínio e U não satisfaz os requisitos específicos de filtragem. A filtragem do atributo não reduz o tempo de importação, mas pode reduzir os tempos de sincronização e exportação.
- **Filtragem baseada em grupo** - utiliza a adesão ao grupo para decidir se os objetos devem ser aprovisionados em Azure AD. A filtragem baseada em grupo só é adequada para situações de teste e não recomendada para a produção, devido às despesas extra necessárias para verificar a adesão ao grupo durante o ciclo de sincronização.

Muitos [objetos de desconexor](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) persistentes no seu Diretório Ativo CS podem causar tempos de sincronização mais longos, porque o motor de provisionamento deve reavaliar cada objeto de desconexão para uma possível ligação no ciclo de sincronização. Para ultrapassar esta questão, considere uma das seguintes recomendações:



- Coloque os objetos de desconexor fora do âmbito de importação utilizando a filtragem de domínio ou u.
- Project/junte os objetos ao MV e detete teo atributo [com cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) igual ao True, para evitar o fornecimento destes objetos no CS AD Azure.

> [!NOTE]
> Os utilizadores podem ficar confusos ou podem ocorrer problemas de permissões de aplicação, quando muitos objetos são filtrados. Por exemplo, numa implementação híbrida do Exchange online, os utilizadores com caixas de correio no local verão mais utilizadores na sua lista de endereços globais do que utilizadores com caixas de correio em Exchange online. Noutros casos, um utilizador pode querer conceder acesso numa aplicação na nuvem a outro utilizador que não faça parte do âmbito do conjunto de objetos filtrado.

### <a name="attribute-flows"></a>Fluxos de atributos

Fluxos de atributos é o processo de cópia ou transformação dos valores atributos de objetos de um diretório conectado para outro diretório conectado. São definidos como parte das regras de sincronização. Por exemplo, quando o número de telefone de um utilizador é alterado no seu Diretório Ativo, o número de telefone em Azure AD será atualizado. As organizações podem [modificar os fluxos de atributos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) para a suite vários requisitos. Recomenda-se que copie os fluxos de atributos existentes antes de os mudar.

Redirecionamentos simples, como fluir um valor de atributo para um atributo diferente não tem impacto no desempenho material. Um exemplo de um redirecionamento é fluir um número de telemóvel no Ative Directory para o número de telefone do escritório em Azure AD.

Transformar valores de atributo pode ter um impacto de desempenho no processo de sincronização. A transformação dos valores dos atributos inclui modificar, reformar, concatenar ou subtrair valores de atributos.

As organizações podem impedir certos atributos de fluir para a AD Azure, mas não influenciará o desempenho do motor de provisionamento.

> [!NOTE]
> Não apague fluxos de atributos indesejados nas suas regras de sincronização. Recomenda-se que os desative, pois as regras eliminadas são recriadas durante as atualizações do Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Fatores de dependência azure AD Connect

O desempenho da Azure AD Connect depende do desempenho dos diretórios conectados para os que importa e exporta. Por exemplo, a dimensão do Diretório Ativo que necessita para importar ou a latência da rede para o serviço Azure AD. A base de dados SQL que o motor de provisionamento utiliza também afeta o desempenho global do ciclo de sincronização.

### <a name="active-directory-factors"></a>Fatores de Diretório Ativo

Como mencionado anteriormente, o número de objetos a importar influencia significativamente o desempenho. O [hardware e os pré-requisitos para o Azure AD Connect](how-to-connect-install-prerequisites.md) delineiam níveis de hardware específicos com base no tamanho da sua implementação. O Azure AD Connect apenas suporta topoologias específicas como delineado em [Topologies para Azure AD Connect](plan-connect-topologies.md). Não existem otimizações de desempenho e recomendações para topologas não suportadas.

Certifique-se de que o seu servidor Azure AD Connect cumpre os requisitos de hardware com base no tamanho do Diretório Ativo que pretende importar. A má ou lenta conectividade da rede entre o servidor Azure AD Connect e os seus controladores de domínio Ative Directory pode abrandar a sua importação.

### <a name="azure-ad-factors"></a>Fatores DaD Azure

A Azure AD usa estrangulamento para proteger o serviço de nuvem de ataques de negação de serviço (DoS). Atualmente a AD Azure tem um limite de estrangulamento de 7.000 escritos por 5 minutos (84.000 por hora). Por exemplo, as seguintes operações podem ser aceleradas:



- Azure AD Connect exporta para Azure AD.
- Scripts ou aplicações PowerShell atualizando o AD Azure diretamente mesmo em segundo plano, tais como membros do grupo Dynamic.
- Os utilizadores atualizam os seus próprios registos de identidade, tais como o registo de MFA ou SSPR (reset de palavra-passe self-service).
- Operações dentro da interface gráfica do utilizador.

Planeie tarefas de implantação e manutenção, para garantir que o seu ciclo de sincronização Azure AD Connect não seja afetado por limites de estrangulamento. Por exemplo, se tiver uma grande onda de contratação onde cria milhares de identidades de utilizador, pode causar atualizações a membros dinâmicos do grupo, atribuições de licenciamento e redefinição de passwords de autosserviço. É melhor espalhar estes escritos ao longo de várias horas ou alguns dias.

### <a name="sql-database-factors"></a>Fatores de base de dados SQL

O tamanho da sua fonte A topologia do Diretório Ativo influenciará o desempenho da sua base de dados SQL. Siga os requisitos de [hardware](how-to-connect-install-prerequisites.md) para a base de dados do servidor SQL e considere as seguintes recomendações:



- Organizações com mais de 100.000 utilizadores podem reduzir as latenciências da rede colocando a base de dados SQL e o motor de provisionamento no mesmo servidor.
- Devido aos requisitos elevados de entrada e saída de disco (I/O) do processo de sincronização, utilize unidades de estado sólido (SSD) para a base de dados SQL do motor de provisionamento para obter resultados ótimos, se não possível, considere as configurações RAID 0 ou RAID 1.
- Não faça uma sincronização completa preventivamente; causa agitação desnecessária e tempos de resposta mais lentos.

## <a name="conclusion"></a>Conclusão

Para otimizar o desempenho da sua implementação azure AD Connect, considere as seguintes recomendações:



- Utilize a configuração de [hardware recomendada](how-to-connect-install-prerequisites.md) com base no tamanho da sua implementação para o servidor Azure AD Connect.
- Ao atualizar o Azure AD Connect em implementações em larga escala, considere utilizar o método de migração do [balanço,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration)para se certificar de que tem menos tempo de inatividade e melhor fiabilidade. 
- Utilize o SSD para a base de dados SQL para melhor desempenho de escrita.
- Filtrar o âmbito do Diretório Ativo para incluir apenas objetos que precisam de ser aprovisionados em AD Azure, utilizando filtro de domínio, OU ou atributo.
- Se necessitar de alterar as regras de fluxo de atributo sinuosos, primeiro copie a regra, em seguida, altere a cópia e desative a regra original. Lembre-se de refazer uma sincronização completa.
- Planeie o tempo adequado para o perfil de execução de sincronização completa inicial.
- Esforce-se para completar o ciclo de sincronização delta em 30 minutos. Se o perfil de sincronização delta não estiver concluído em 30 minutos, modifique a frequência de sincronização padrão para incluir um ciclo completo de sincronização delta.
- Monitorize a sua [saúde de sincronização Azure AD Connect](how-to-connect-health-agent-install.md) em Azure AD.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
