---
title: Fatores a influenciar o desempenho do Azure AD Connect
description: Este documento explica como vários fatores influenciam o motor de provisionamento Azure AD Connect. Estes fatores ajudarão as organizações a planear a sua implementação Azure AD Connect para garantir que cumpre os seus requisitos de sincronização.
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
ms.openlocfilehash: 15bcb0f7ca30c343072da396abeac8d08dee03a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90087014"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Fatores a influenciar o desempenho do Azure AD Connect

A Azure AD Connect sincroniza o seu Ative Directory para Azure AD. Este servidor é um componente crítico de mover as identidades do utilizador para a nuvem. Os principais fatores que afetam o desempenho de um Azure AD Connect são:

| **Fator de design**| **Definição** |
|:-|-|
| Topologia| A distribuição dos pontos finais e componentes que o Azure AD Connect deve gerir na rede. |
| Escala| O número de objetos como os utilizadores, grupos e OUs, a ser gerido pelo Azure AD Connect. |
| Hardware| O hardware (físico ou virtual) para o Azure AD Connect e a capacidade de desempenho dependente de cada componente de hardware, incluindo CPU, memória, rede e configuração de disco rígido. |
| Configuração| Como a Azure AD Connect processa os diretórios e informações. |
| Carregamento| A frequência das mudanças de objetos. As cargas podem variar durante uma hora, dia ou semana. Dependendo do componente, poderá ter de desenhar para a carga máxima ou para a carga média. |

O objetivo deste documento é descrever os fatores que influenciam o desempenho do motor de provisionamento Azure AD Connect. Organizações grandes ou complexas (organizações que aforem mais de 100.000 objetos) podem usar as recomendações para otimizar a sua implementação Azure AD Connect, se experimentarem quaisquer problemas de desempenho delineados aqui. Os outros componentes do Azure AD Connect, como [o Azure AD Connect health](how-to-connect-health-agent-install.md) e os agentes não estão cobertos aqui.

> [!IMPORTANT]
> A Microsoft não suporta modificar ou operar o Azure AD Connect fora das ações que estão formalmente documentadas. Qualquer uma destas ações pode resultar num estado inconsistente ou não suportado da sincronização Azure AD Connect. Como resultado, a Microsoft não pode fornecer suporte técnico para tais implementações.

## <a name="azure-ad-connect-component-factors"></a>Fatores componentes AZURE AD Connect

O diagrama seguinte mostra uma arquitetura de alto nível de motor de provisionamento conectando-se a uma única floresta, embora várias florestas são apoiadas. Esta arquitetura mostra como os vários componentes interagem uns com os outros.

![O diagrama mostra como os Diretórios Conectados e o motor de provisionamento AD AD Connect interagem, incluindo componentes do Espaço do Conector e metaverso numa base de dados SQL. ](media/plan-connect-performance-factors/AzureADConnentInternal.png)

O motor de provisionamento liga-se a cada floresta ative de Diretório e a Azure AD. O processo de leitura de informação de cada diretório chama-se Import. Exportação refere-se à atualização dos diretórios do motor de avisão. O Sync avalia as regras de como os objetos fluirão dentro do motor de provisionamento. Para um mergulho mais profundo, pode consultar a [sincronização Azure AD Connect: Compreender a arquitetura](./concept-azure-ad-connect-sync-architecture.md).

O Azure AD Connect utiliza as seguintes áreas de preparação, regras e processos para permitir a sincronização do Ative Directory para a Azure AD:

* **Connector Space (CS)** - Os objetos de cada diretório conectado (CD), os diretórios reais, são encenados aqui primeiro antes de poderem ser processados pelo motor de provisionamento. A Azure AD tem o seu próprio CS e cada floresta a que se conecta tem o seu próprio CS.
* **Metaverso (MV)** - Os objetos que precisam de ser sincronizados são criados aqui com base nas regras de sincronização. Os objetos devem existir no MV antes de poderem povoar objetos e atributos aos outros diretórios ligados. Só há um MV.
* **Regras de sincronização** - Decidem quais os objetos que serão criados (projetados) ou ligados (unidos) a objetos no MV. As regras de sincronização também decidem quais os valores de atributos que serão copiados ou transformados de e para os diretórios.
* **Executar perfis** - Agrega as etapas de processo de copiar objetos e os seus valores de atributos de acordo com as regras de sincronização entre as áreas de preparação e diretórios conectados.

Existem diferentes perfis de execução para otimizar o desempenho do motor de provisionamento. A maioria das organizações usará os horários padrão e executará perfis para operações normais, mas algumas organizações podem ter que [alterar o horário](./how-to-connect-sync-feature-scheduler.md) ou desencadear outros perfis de execução para atender a situações incomuns. Estão disponíveis os seguintes perfis de execução:

### <a name="initial-sync-profile"></a>Perfil de sincronização inicial

O perfil de sincronização inicial é o processo de leitura dos diretórios conectados, como uma floresta de Diretório Ativo, pela primeira vez. Em seguida, faz uma análise de todas as entradas na base de dados do motor de sincronização. O ciclo inicial criará novos objetos em Azure AD e levará tempo extra para ser concluído se as suas florestas de Diretório Ativo forem grandes. A sincronização inicial inclui os seguintes passos:

1. Importação completa em todos os conectores
2. Sincronização completa em todos os conectores
3. Exportação em todos os conectores

### <a name="delta-sync-profile"></a>Perfil de sincronização delta

Para otimizar o processo de sincronização, este perfil de execução apenas processa as alterações (cria, elimina e atualiza) objetos nos seus diretórios conectados, desde o último processo de sincronização. Por predefinição, o perfil de sincronização delta funciona a cada 30 minutos. As organizações devem esforçar-se para manter o tempo que leva para menos de 30 minutos, para garantir que o AD Azure está atualizado. Para monitorizar a saúde do Azure AD Connect, utilize o [agente de monitorização de saúde](how-to-connect-health-sync.md) para ver quaisquer problemas com o processo. O perfil de sincronização delta inclui os seguintes passos:

1. Importação delta em todos os conectores
2. Sincronização delta em todos os conectores
3. Exportação em todos os conectores

Um cenário típico de sincronização delta da organização empresarial é:

- ~1% dos objetos são eliminados
- ~1% dos objetos são criados
- ~5% dos objetos são modificados

A sua taxa de variação pode variar dependendo da frequência com que a sua organização atualiza os utilizadores no seu Ative Directory. Por exemplo, taxas de mudança mais elevadas podem ocorrer com a sazonalidade da contratação e redução da força de trabalho.

### <a name="full-sync-profile"></a>Perfil sincronizado completo

É necessário um ciclo de sincronização completo se tiver escamado alguma das seguintes alterações de configuração:



- Aumentou o âmbito dos objetos ou atributos a serem importados dos diretórios ligados. Por exemplo, quando adiciona um domínio ou OU ao seu âmbito de importação.
- Fez alterações nas regras de sincronização. Por exemplo, quando cria uma nova regra para preencher o título de um utilizador em Azure AD a partir de extension_attribute3 no Ative Directory. Esta atualização requer que o motor de provisionamento reexamine todos os utilizadores existentes para atualizar os seus títulos para aplicar a mudança em curso.

As seguintes operações estão incluídas num ciclo de sincronização completa:

1. Importação completa em todos os conectores
2. Sincronização completa/Delta em todos os conectores
3. Exportação em todos os conectores

> [!NOTE]
> É necessário um planeamento cuidadoso ao fazer atualizações a granel a muitos objetos no seu Ative Directory ou Azure AD. As atualizações a granel farão com que o processo de sincronização delta desemoque mais tempo ao importar, uma vez que muitos objetos mudaram. As importações longas podem acontecer mesmo que a atualização em massa não influencie o processo de sincronização. Por exemplo, a atribuição de licenças a muitos utilizadores em Azure AD irá causar um longo ciclo de importação a partir da Azure AD, mas não resultará em alterações de atributos no Ative Directory.

### <a name="synchronization"></a>Sincronização

O tempo de execução do processo de sincronização tem as seguintes características de desempenho:

* O sync é único roscado, o que significa que o motor de provisionamento não faz qualquer processamento paralelo de perfis de execução de diretórios, objetos ou atributos conectados.
* O tempo de importação cresce linearmente com o número de objetos a ser sincronizado. Por exemplo, se 10.000 objetos demorarem 10 minutos a importar, então 20.000 objetos demorarão aproximadamente 20 minutos no mesmo servidor.
* A exportação também é linear.
* A sincronização crescerá exponencialmente com base no número de objetos com referências a outros objetos. Os membros do grupo e os grupos aninhados têm o principal impacto no desempenho, porque os seus membros referem-se a objetos de utilizador ou outros grupos. Estas referências devem ser encontradas e referenciadas a objetos reais no MV para completar o ciclo de sincronização.

### <a name="filtering"></a>Filtragem

A dimensão da topologia do Ative Directory que pretende importar é o fator número um que influencia o desempenho e o tempo geral que os componentes internos do motor de avisão tomarão.

[A filtragem](./how-to-connect-sync-configure-filtering.md) deve ser utilizada para reduzir os objetos para a sincronização. Evitará que objetos desnecessários sejam processados e exportados para Azure AD. Por ordem de preferência, estão disponíveis as seguintes técnicas de filtragem:



- **Filtragem baseada em domínios** – utilize esta opção para selecionar domínios específicos para sincronizar com a Azure AD. Tem de adicionar e remover domínios da configuração do motor de sincronização quando escruisse as alterações na sua infraestrutura no local depois de instalar a sincronização do Azure AD Connect.
- **Filtragem da Unidade de Organização (OU)** - utiliza OUs para direcionar objetos específicos em domínios de Diretório Ativo para provisão ao Azure AD. A filtragem da UA é o segundo mecanismo de filtragem recomendado, porque utiliza consultas de âmbito LDAP simples para importar um subconjunto menor de objetos do Ative Directory.
- **Attribute a filtragem por objeto** - utiliza os valores do atributo em objetos para decidir se o objeto específico no Ative Directory é adsatado em Azure AD. A filtragem de atributos é ótima para afinar os filtros, quando o domínio e a filtragem da U não satisfazem os requisitos específicos de filtragem. A filtragem do atributo não reduz o tempo de importação, mas pode reduzir os tempos de sincronização e exportação.
- **Filtragem baseada em grupo** - usa a filiação do grupo para decidir se os objetos devem ser a provisionados em Azure AD. A filtragem baseada em grupo é adequada apenas para situações de teste e não recomendada para produção, devido às despesas adicionais necessárias para verificar a adesão ao grupo durante o ciclo de sincronização.

Muitos objetos de [desconexão persistentes](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) no seu CS ative diretor podem causar tempos de sincronização mais longos, porque o motor de provisionamento deve reavaliar cada objeto de desconexão para uma possível ligação no ciclo de sincronização. Para ultrapassar esta questão, considere uma das seguintes recomendações:



- Coloque os objetos desconexão fora do alcance para importação utilizando o domínio ou a filtragem da U.
- Projetar/juntar os objetos ao MV e definir o atributo [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) igual ao True, para evitar o provisionamento destes objetos no Azure AD CS.

> [!NOTE]
> Os utilizadores podem ficar confusos ou podem ocorrer problemas de permissões de aplicação, quando muitos objetos são filtrados. Por exemplo, numa implementação online híbrida do Exchange, os utilizadores com caixas de correio no local verão mais utilizadores na sua lista de endereços globais do que os utilizadores com caixas de correio em Exchange online. Noutros casos, um utilizador pode querer conceder acesso numa aplicação em nuvem a outro utilizador que não faça parte do âmbito do conjunto filtrado de objetos.

### <a name="attribute-flows"></a>Fluxos de atributos

Os fluxos de atributos são o processo de cópia ou transformação dos valores de atributos de objetos de um diretório ligado a outro diretório ligado. São definidos como parte das regras de sincronização. Por exemplo, quando o número de telefone de um utilizador for alterado no seu Diretório Ativo, o número de telefone em Azure AD será atualizado. As organizações podem [modificar os fluxos de atributos](./how-to-connect-sync-change-the-configuration.md) para suíte de vários requisitos. Recomenda-se que copie os fluxos de atributos existentes antes de os alterar.

Redirecionamentos simples, como fluir um valor de atributo para um atributo diferente não tem impacto material no desempenho. Um exemplo de um redirecionamento é o fluxo de um número de telemóvel no Ative Directory para o número de telefone do escritório em Azure AD.

Transformar os valores dos atributos pode ter um impacto de desempenho no processo de sincronização. A transformação dos valores dos atributos inclui modificar, reformar, concatenar ou subtrair valores de atributos.

As organizações podem impedir que certos atributos fluam para a Azure AD, mas não influenciarão o desempenho do motor de provisionamento.

> [!NOTE]
> Não elimine fluxos de atributos indesejados nas suas regras de sincronização. Recomenda-se que os desative, porque as regras eliminadas são recriadas durante as atualizações do Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect fatores de dependência

O desempenho do Azure AD Connect depende do desempenho dos diretórios ligados para os qual importa e exporta. Por exemplo, a dimensão do Diretório Ativo que necessita para importar ou a latência da rede para o serviço Azure AD. A base de dados SQL que o motor de provisionamento utiliza também afeta o desempenho global do ciclo de sincronização.

### <a name="active-directory-factors"></a>Fatores de diretório ativo

Como mencionado anteriormente, o número de objetos a importar influencia significativamente o desempenho. O [hardware e os pré-requisitos para Azure AD Connectlineline](how-to-connect-install-prerequisites.md) níveis de hardware específicos baseados no tamanho da sua implementação. O Azure AD Connect suporta apenas topologias específicas, tal como delineadas em [Topologias para Azure AD Connect](plan-connect-topologies.md). Não existem otimizações de desempenho e recomendações para topologias não suportadas.

Certifique-se de que o seu servidor AZURE AD Connect satisfaz os requisitos de hardware baseados no tamanho do Seu Diretório Ativo que pretende importar. A conectividade de rede má ou lenta entre o servidor Azure AD Connect e os controladores de domínio do Ative Directory pode abrandar a sua importação.

### <a name="azure-ad-factors"></a>Fatores AD Azure

A Azure AD usa estrangulamento para proteger o serviço de nuvem de ataques de negação de serviço (DoS). Atualmente, a Azure AD tem um limite de estrangulamento de 7.000 escritos por 5 minutos (84.000 por hora). Por exemplo, as seguintes operações podem ser aceleradas:



- Azure AD Connect exportação para Azure AD.
- Scripts ou aplicações PowerShell atualizando o AD Azure diretamente em segundo plano, tais como membros do grupo Dynamic.
- Os utilizadores atualizam os seus próprios registos de identidade, tais como o registo de MFA ou SSPR (redefinição da palavra-passe de autosserviço).
- Operações dentro da interface gráfica do utilizador.

Planeie tarefas de implantação e manutenção, para garantir que o seu ciclo de sincronização Azure AD Connect não seja afetado por limites de estrangulamento. Por exemplo, se tiver uma grande onda de contratação onde cria milhares de identidades de utilizador, pode causar atualizações a membros dinâmicos do grupo, atribuições de licenciamento e registos de redefinição de passwords de autosserviço. É melhor espalhar estes escritos por várias horas ou alguns dias.

### <a name="sql-database-factors"></a>Fatores de base de dados SQL

O tamanho da sua topologia de diretório ativo de origem irá influenciar o desempenho da sua base de dados SQL. Siga os [requisitos de hardware](how-to-connect-install-prerequisites.md) para a base de dados do servidor SQL e considere as seguintes recomendações:



- Organizações com mais de 100.000 utilizadores podem reduzir as latências da rede colocando a base de dados SQL e o motor de provisionamento no mesmo servidor.
- Devido aos elevados requisitos de entrada e saída de disco (I/O) do processo de sincronização, utilize as Unidades de Estado Sólido (SSD) para a base de dados SQL do motor de avisão para obter resultados ótimos, se não possível, considere as configurações RAID 0 ou RAID 1.
- Não faça uma sincronização completa preventivamente; causa agitação desnecessária e tempos de resposta mais lentos.

## <a name="conclusion"></a>Conclusão

Para otimizar o desempenho da sua implementação Azure AD Connect, considere as seguintes recomendações:



- Utilize a [configuração de hardware recomendada](how-to-connect-install-prerequisites.md) com base no tamanho da sua implementação para o servidor Azure AD Connect.
- Ao atualizar o Azure AD Connect em implementações em larga escala, considere utilizar o [método de migração do baloiço,](./how-to-upgrade-previous-version.md#swing-migration)para se certificar de que tem o menor tempo de inatividade e a melhor fiabilidade. 
- Utilize SSD para a base de dados SQL para obter um melhor desempenho de escrita.
- Filtrar o âmbito do Diretório Ativo apenas para incluir objetos que precisam de ser a provisionados em AZure AD, utilizando domínio, OU ou filtragem de atributos.
- Se necessitar de alterar as regras de fluxo de atributos predefinidos, primeiro copie a regra, em seguida, altere a cópia e desative a regra original. Lembre-se de refazer uma sincronização completa.
- Planeie um tempo adequado para o perfil inicial de sincronização completa.
- Esforce-se para completar o ciclo de sincronização delta em 30 minutos. Se o perfil de sincronização delta não estiver completo em 30 minutos, modifique a frequência de sincronização padrão para incluir um ciclo completo de sincronização delta.
- Monitorize a sua [saúde de sincronização Azure AD Connect](how-to-connect-health-agent-install.md) em Azure AD.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).