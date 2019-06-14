---
title: Fatores a influenciar o desempenho do Azure AD Connect
description: Este documento explica os fatores como várias influenciam o Azure AD Connect mecanismo de provisionamento. Esses fatores ajudará as organizações a planejar a implantação do Azure AD Connect para se certificar de que cumpre os requisitos de sincronização.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a3a57fbe5df690e4dbdba8cbab85e62648bb298
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60295382"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Fatores a influenciar o desempenho do Azure AD Connect

O Azure AD Connect sincroniza o seu Active Directory para o Azure AD. Esse servidor é um componente crítico de mover as identidades de utilizador para a cloud. São os principais fatores que afetam o desempenho de um Azure AD Connect:

| **Fator da estrutura**| **Definição** |
|:-|-|
| Topologia| A distribuição dos pontos de extremidade e componentes do Azure AD Connect tem de gerir na rede. |
| Escala| O número de objetos, como os utilizadores, grupos e UOs, para serem geridos pelo Azure AD Connect. |
| Hardware| O hardware (físico ou virtual) para o Azure AD Connect e a capacidade de desempenho dependentes de cada componente de hardware, incluindo a CPU, memória, rede e configuração de disco rígido. |
| Configuração| Como o Azure AD Connect processos os diretórios e informações. |
| Carregamento| Frequência de alterações de objetos. As cargas podem variar durante uma hora, dia ou semana. Dependendo do componente, poderá ter de criar para o pico de carga ou a carga média. |

O objetivo deste documento é descrever os fatores influenciar o desempenho do Azure AD Connect mecanismo de provisionamento. As organizações de grandes ou complexas (organizações mais de 100 000 objetos de aprovisionamento) podem utilizar as recomendações para otimizar a sua implementação do Azure AD Connect, se se deparar com problemas de desempenho descritos aqui. Os outros componentes do Azure AD Connect, tal como [do Azure AD Connect health](how-to-connect-health-agent-install.md) e agentes não são abordados aqui.

> [!IMPORTANT]
> A Microsoft não suporta a modificação ou operação do Azure AD Connect fora das ações anteriormente documentadas. Qualquer destas ações pode resultar num estado inconsistente ou não suportado da sincronização do Azure AD Connect. Como resultado, a Microsoft não pode possível fornecer o suporte técnico para implementações deste tipo.

## <a name="azure-ad-connect-component-factors"></a>Fatores de componente do Azure AD Connect

O diagrama seguinte mostra uma arquitetura de alto nível do aprovisionamento de mecanismo de ligação a uma única floresta, apesar de várias florestas são suportadas. Esta arquitetura mostra como os vários componentes interagem entre si.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

O mecanismo de provisionamento liga-se para cada floresta do Active Directory e para o Azure AD. O processo de leitura de informações de cada diretório é chamado de importação. Exportação refere-se para atualizar os diretórios do mecanismo de provisionamento. Sincronização avalia as regras de como os objetos irão fluir dentro do mecanismo de provisionamento. Para se aprofundar pode consultar [do Azure AD Connect: Compreendendo a arquitetura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

O Azure AD Connect utiliza as seguintes áreas de preparo, regras e processos para permitir a sincronização do Active Directory para o Azure AD:

* **Espaço conector (CS)** -objetos a partir de cada diretório ligado (CD), os diretórios reais, são armazenadas temporariamente aqui pela primeira vez antes que possam ser processados pelo mecanismo de provisionamento. O Azure AD tem seu próprio CS e cada floresta que se liga a tem seu próprio CS.
* **Metaverso (MV)** -objetos que precisam ser sincronizados são criar aqui com base nas regras de sincronização. Objetos têm de existir na MV antes de eles podem povoar objetos e atributos para outros diretórios conectados. Há apenas uma MV.
* **Regras de sincronização** -eles decidirem quais os objetos serão criados (previstos) ou ligados (associado ao) para objetos na MV. As regras de sincronização também decidir quais valores de atributo serão copiados ou transformados de e para os diretórios.
* **Perfis de execução** -agrupa as etapas do processo de cópia de objetos e os respetivos valores de atributo de acordo com as regras de sincronização entre as áreas de preparo e diretórios conectados.

Perfis de execução diferentes existem para otimizar o desempenho do mecanismo de provisionamento. A maioria das organizações irá utilizar as agendas predefinidas e executar perfis para as operações normais, mas algumas organizações podem ter para [alterar a agenda](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) ou acionar outro perfis de execução para se adaptar para situações invulgares. Os seguintes perfis de execução estão disponíveis:

### <a name="initial-sync-profile"></a>Perfil de sincronização inicial

O perfil de sincronização inicial é o processo de leitura de diretórios conectados, como uma floresta do Active Directory, pela primeira vez. Em seguida, faz uma análise em todas as entradas do banco de dados do motor de sincronização. O ciclo inicial irá criar novos objetos no Azure AD e levará tempo extra para concluir se florestas do Active Directory são grandes. A sincronização inicial inclui os seguintes passos:

1. Importação completa em todos os conectores
2. Sincronização completa em todos os conectores
3. Exportação em todos os conectores

### <a name="delta-sync-profile"></a>Perfil de sincronização delta

Para otimizar o processo de sincronização este perfil de execução apenas processar as alterações (cria, elimina e atualiza) de objetos nos seus diretórios conectados, desde o último processo de sincronização. Por predefinição, o perfil de sincronização de diferenças é executada a cada 30 minutos. As organizações devem se esforçar para manter o tempo que demora a abaixo de 30 minutos, para se certificar de que o Azure AD está atualizado. Para monitorizar o estado de funcionamento do Azure AD Connect, utilize o [estado de funcionamento do agente de monitorização](how-to-connect-health-sync.md) para identificar quaisquer problemas com o processo. O perfil de sincronização delta inclui os seguintes passos:

1. Importação delta em todos os conectores
2. Sincronização delta em todos os conectores
3. Exportação em todos os conectores

Um cenário de sincronização de diferenças de organização empresarial típico é:

- % de ~ 1 dos objetos são eliminados
- % de ~ 1 dos objetos são criados
- ~ 5% dos objetos são modificados

A taxa de alteração pode variar consoante a frequência com que os utilizadores no Active Directory de atualizações de sua organização. Por exemplo, as taxas mais elevadas de alteração podem ocorrer no sazonalidade de contratação e reduzir a força de trabalho.

### <a name="full-sync-profile"></a>Perfil de sincronização completa

Um ciclo de sincronização completa é necessário se tiver efetuado qualquer uma das seguintes alterações de configuração:



- Aumentou o âmbito dos objetos ou atributos a ser importados a partir de diretórios conectados. Por exemplo, quando adiciona um domínio ou UO para o seu âmbito de importação.
- Efetuou alterações para as regras de sincronização. Por exemplo, quando cria uma nova regra para preencher o título de um utilizador no Azure AD a partir de extension_attribute3 no Active Directory. Esta atualização requer que o mecanismo de provisionamento examinar novamente todos os utilizadores existentes para atualizar seus títulos para aplicar a alteração no futuro.

As seguintes operações estão incluídas num ciclo de sincronização completa:

1. Importação completa em todos os conectores
2. Sincronização completa/Delta em todos os conectores
3. Exportação em todos os conectores

> [!NOTE]
> É necessário um planejamento cuidadoso ao realizar atualizações em massa para muitos objetos no seu Active Directory ou o Azure AD. Atualizações em massa fará com que o processo de sincronização delta demore mais tempo ao importar, uma vez que muitos objetos foram alterados. Importações longo podem acontecer mesmo que a atualização em massa não influenciam o processo de sincronização. Por exemplo, atribuir licenças a utilizadores no Azure AD fará com que um ciclo de longo importação do Azure AD, mas não resultará em alterações de atributo no Active Directory.

### <a name="synchronization"></a>Sincronização

O tempo de execução do processo de sincronização tem as seguintes características de desempenho:

* Sincronização é o única thread, que significa que o mecanismo de provisionamento não faz qualquer processamento paralelo dos perfis de execução de diretórios conectados, objetos ou atributos.
* Tempo de importação aumenta linearmente com o número de objetos a ser sincronizado. Por exemplo, se 10 000 objetos demorar 10 minutos para importar, 20.000 objetos levará cerca de 20 minutos no mesmo servidor.
* A exportação também é linear.
* A sincronização irá aumentar exponencialmente com base no número de objetos com referências a outros objetos. Membros de grupo e de grupos aninhados têm o impacto no desempenho principal, porque seus membros fazem referência a objetos de utilizador ou outros grupos. Estas referências tem de ser encontradas e referenciadas para objetos reais na MV para concluir o ciclo de sincronização.

### <a name="filtering"></a>Filtering

O tamanho da topologia do Active Directory que pretende importar é o número um fator de influenciar o desempenho e o tempo total que os componentes internos do mecanismo de provisionamento irão demorar.

[Filtragem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) deve ser usado para reduzir os objetos para a sincronização. Ele irá impedir que objetos desnecessários seja processada e exportado para o Azure AD. Por ordem de preferência, as seguintes técnicas de filtragem estão disponíveis:



- **Filtragem baseada em domínio** – Utilize esta opção para selecionar domínios específicos para sincronizar com o Azure AD. Tem de adicionar e remover domínios da configuração do motor de sincronização quando fizer alterações à sua infraestrutura no local depois de instalar a sincronização do Azure AD Connect.
- **Filtragem de UO (unidade) de organização** -utiliza UOs para objetos específicos nos domínios do Active Directory para o aprovisionamento para o Azure AD de destino. Filtragem de UO é o segundo recomendado filtragem mecanismo, porque utiliza consultas de âmbito simples de LDAP para importar um subconjunto mais pequeno de objetos do Active Directory.
- **Atributo filtragem por objeto** -usa os valores de atributo em objetos para decidir se o objeto específico no Active Directory é aprovisionado no Azure AD. Filtragem de atributos é excelente para ajustar os filtros, quando a filtragem de UO de domínio e não cumpre os requisitos específicos de filtragem. Filtragem de atributos não reduzir o tempo de importação, mas pode reduzir a sincronização e exportar vezes.
- **Filtragem baseada em grupo** -utiliza grupo associação para decidir se os objetos devem ser aprovisionados no Azure AD. Filtragem baseada em grupo é apenas adequada para situações de teste e não recomendado para produção, devido a extra necessário sobrecarga para verificar a associação ao grupo durante o ciclo de sincronização.

Muitos persistente [objetos de seccionador](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) no seu CS de diretório Active Directory pode fazer com que períodos de sincronização, uma vez que o mecanismo de provisionamento deve reavaliar a cada objeto de seccionador para ligação possível no ciclo de sincronização. Para superar esse problema, considere um dos seguintes recomendações:



- Coloque os objetos de seccionador fora do âmbito para importar com o domínio ou a filtragem de UO.
- Projeto/junção os objetos para a MV e defina a [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) atributo igual a True, para impedir que o aprovisionamento de um desses objetos no Azure AD CS.

> [!NOTE]
> Os utilizadores podem ficar confuso ou podem ocorrer problemas de permissões de aplicação, quando são filtrados demasiados objetos. Por exemplo, numa Exchange online implementação híbrida, os utilizadores com caixas de correio no local Verão mais usuários na lista de endereços global do que os utilizadores com caixas de correio no Exchange online. Em outros casos, um utilizador poderá querer conceder o acesso num aplicativo em nuvem a outro utilizador que não faz parte do âmbito do conjunto filtrado de objetos.

### <a name="attribute-flows"></a>Fluxos de atributos

Fluxos de atributos é o processo para copiar ou transformar os valores de atributos de objetos de um diretório ligado a outro diretório conectado. Estão definidos como parte das regras de sincronização. Por exemplo, quando o número de telefone de um utilizador é alterado no Active Directory, será possível atualizar o número de telefone no Azure AD. As organizações podem [modificar os fluxos de atributos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) ao suite diversos requisitos. Recomenda-se que copiar os fluxos de atributos existentes antes de alterá-los.

Redirecionamentos simples, como um valor de atributo para um atributo diferente a fluir não tem impacto no desempenho material. Um exemplo de um redirecionamento está a ser encaminhados um número de telemóvel no Active Directory para o número de telefone do office no Azure AD.

Transformar valores de atributo pode ter um impacto no desempenho do processo de sincronização. Transformar valores de atributo inclui a modificação, reformatação, concatenando ou subtração de valores de atributos.

As organizações podem impedir que certos atributos de fluir para o Azure AD, mas ele não influenciam o desempenho do mecanismo de provisionamento.

> [!NOTE]
> Não elimine fluxos de atributos indesejados nas suas regras de sincronização. Recomenda-se em vez disso, desativá-las, porque as regras eliminadas são recriadas durante as atualizações do Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Fatores de dependência do Azure AD Connect

O desempenho do Azure AD Connect é depende do desempenho dos diretórios conectados, importa e exporta para. Por exemplo, o tamanho do Active Directory tem de importar ou a latência de rede para o serviço do Azure AD. A base de dados SQL que utiliza o mecanismo de provisionamento também afeta o desempenho geral do ciclo de sincronização.

### <a name="active-directory-factors"></a>Fatores de diretório Active Directory

Como mencionado anteriormente, o número de objetos a serem importados influencia significativamente o desempenho. O [hardware e pré-requisitos do Azure AD Connect](how-to-connect-install-prerequisites.md) descrevem as camadas de hardware específico com base no tamanho da sua implementação. O Azure AD Connect só suportam topologias específicas, conforme descrito na [topologias do Azure AD Connect](plan-connect-topologies.md). Não existem otimizações de desempenho e recomendações para topologias não suportadas.

Certifique-se de que o servidor do Azure AD Connect cumpre os requisitos de hardware com base no tamanho do seu Active Directory que pretende importar. Conectividade de rede lenta ou ruim entre o servidor do Azure AD Connect e os controladores de domínio do Active Directory pode abrandar a importação.

### <a name="azure-ad-factors"></a>Fatores de AD do Azure

Azure AD utiliza a limitação para proteger o serviço em nuvem de ataques do denial-of-service (DoS). Atualmente o Azure AD tem um limite de limitação de 7.000 escritas por 5 minutos (84,000 por hora). Por exemplo, podem ser otimizadas as seguintes operações:



- O Azure AD Connect a exportação para o Azure AD.
- Scripts do PowerShell ou aplicações a atualizar o Azure AD diretamente até mesmo em segundo plano, como associações de grupo dinâmico.
- Utilizadores a atualizar seus próprios registos de identidade, como a registar para MFA ou SSPR (reposição de palavra-passe self-service).
- Operações na interface gráfica do usuário.

Planear tarefas de implantação e manutenção, para se certificar de que o seu ciclo de sincronização do Azure AD Connect não é afetado pela limitação de limites. Por exemplo, se tiver uma grande onda de contratação onde criar milhares de identidades de utilizador, ele pode fazer com que as atualizações para associações de grupo dinâmico, atribuições, de licenciamento e registos de reposição de palavra-passe self-service. É melhor distribuir essas escritas por várias horas ou alguns dias.

### <a name="sql-database-factors"></a>Fatores de base de dados SQL

O tamanho da topologia do Active Directory de origem irão influenciar o desempenho de base de dados SQL. Siga os [requisitos de hardware](how-to-connect-install-prerequisites.md) para o SQL server da base de dados e considere as seguintes recomendações:



- As organizações com mais de 100 000 utilizadores podem reduzir latências de rede ao colocalizar a base de dados do SQL e o mecanismo de provisionamento no mesmo servidor.
- Devido ao disco de alto de entrada e saída requisitos (e/s) do processo de sincronização, usar unidades de estado sólido (SSD) para a base de dados SQL do mecanismo de provisionamento para obter melhores resultados, se não é possível, considere RAID 0 ou RAID 1 configurações.
- Não fazer uma sincronização completa preventivamente; ele faz com que alterações desnecessárias e tempos de resposta mais lentos.

## <a name="conclusion"></a>Conclusão

Para otimizar o desempenho da sua implementação do Azure AD Connect, considere as seguintes recomendações:



- Utilize o [configuração de hardware recomendada](how-to-connect-install-prerequisites.md) com base no seu tamanho de implementação para o servidor do Azure AD Connect.
- Ao atualizar o Azure AD Connect, nas implementações em larga escala, considere usar [girar o método de migração](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration), para se certificar de que tem o menor tempo de inatividade e melhor fiabilidade. 
- Utilize o SSD para a base de dados SQL para melhor desempenho de gravação.
- Filtre o âmbito do Active Directory para incluir apenas os objetos que têm de ser aprovisionado no Azure AD, com o domínio, a UO ou a filtragem de atributos.
- Se precisar de alterar as regras de fluxo de atributo predefinidas, primeiro copiar a regra, em seguida, altere a cópia e desativar a regra original. Não se esqueça de voltar a executar uma sincronização completa.
- Planear o tempo suficiente para a sincronização completa inicial, perfil de execução.
- Se esforça concluir o ciclo de sincronização de diferenças em 30 minutos. Se o perfil de sincronização delta não concluída em 30 minutos, modificar a frequência de sincronização padrão para incluir um ciclo de sincronização delta completa.
- Monitor de sua [estado de funcionamento do Azure AD Connect sync](how-to-connect-health-agent-install.md) no Azure AD.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
