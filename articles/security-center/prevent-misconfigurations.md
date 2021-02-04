---
title: Como prevenir erros de configuração com o Centro de Segurança Azure
description: Saiba como utilizar as opções de "Enforce" e 'Deny' do Security Center nas páginas de detalhes das recomendações
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558189"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Evitar configurações incorretas com recomendações Impor/Negar

As configurações erradas de segurança são a principal causa de incidentes de segurança. O Centro de Segurança tem agora a capacidade de ajudar a *prevenir* a má configuração de novos recursos no que diz respeito a recomendações específicas. 

Esta função pode ajudar a manter as suas cargas de trabalho seguras e estabilizar a sua pontuação segura.

A aplicação de uma configuração segura, baseada numa recomendação específica, é oferecida em dois modos:

- Usando o efeito **Deny** da Política Azure, você pode impedir que recursos insalubres sejam criados
- Utilizando a opção **Enforce,** pode tirar partido do efeito **DeployIfNotExist** da política da Azure e remediar automaticamente recursos não conformes após a criação

Isto pode ser encontrado no topo da página de detalhes de recursos para recomendações de segurança selecionadas (ver [Recomendações com opções de negação/execução).](#recommendations-with-denyenforce-options)

## <a name="prevent-resource-creation"></a>Prevenir a criação de recursos

1. Abra a recomendação de que os seus novos recursos devem satisfazer e selecione o botão **Deny** no topo da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Página de recomendação com botão Deny realçado":::

    O painel de configuração abre a listagem das opções de âmbito. 

1. Desacione o âmbito selecionando o grupo de subscrição ou gestão relevante.

    > [!TIP]
    > Pode utilizar os três pontos no final da linha para alterar uma única subscrição, ou utilizar as caixas de verificação para selecionar várias subscrições ou grupos e, em seguida, selecionar **Alterar para Negar**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Definir a margem de manobra para a Política Azure negar":::


## <a name="enforce-a-secure-configuration"></a>Impor uma configuração segura

1. Abra a recomendação de que irá implementar uma implementação de modelo para se novos recursos não satisfazê-lo, e selecione o botão **Enforce** no topo da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Página de recomendação com botão De aplicação realçado":::

    O painel de configuração abre-se com todas as opções de configuração de política. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Impor opções de configuração":::

1. Desa esta vista, nome de atribuição e outras opções relevantes.

1. Selecione **Rever + criar**.

## <a name="recommendations-with-denyenforce-options"></a>Recomendações com opções de negação/aplicação

Estas recomendações podem ser utilizadas com a opção **de negação:**

- O acesso a contas de armazenamento com firewall e configurações de rede virtuais deve ser restringido
- Azure Cache para Redis deve residir dentro de uma rede virtual
- As contas DB da Azure Cosmos devem usar chaves geridas pelo cliente para encriptar dados em repouso
- Os espaços de trabalho de aprendizagem automática Azure devem ser encriptados com uma chave gerida pelo cliente (CMK)
- Azure Spring Cloud deve usar injeção de rede
- As contas de Serviços Cognitivos devem permitir a encriptação de dados com uma chave gerida pelo cliente (CMK)
- CpU do contentor e limites de memória devem ser aplicados
- As imagens dos contentores devem ser implantadas apenas a partir de registos fidedignos
- Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente (CMK)
- Contentor com escalada de privilégio deve ser evitado
- Os recipientes que partilham espaços sensíveis de nome de hospedeiro devem ser evitados
- Os contentores devem ouvir apenas as portas permitidas
- O sistema de ficheiros de raiz imutável (apenas para leitura) deve ser aplicado para os contentores
- As chaves do cofre devem ter uma data de validade
- Segredos chave do cofre devem ter uma data de validade
- Os cofres-chave devem ter proteção de purga ativada
- As abóbadas-chave devem ter a eliminação suave ativada
- Capacidades linux menos privilegiadas devem ser aplicadas para contentores
- Apenas devem ser ativadas ligações seguras à sua Cache Redis
- Sobrevaor ou desativação de contentores O perfil appArmor deve ser restringido
- Recipientes privilegiados devem ser evitados
- Os recipientes de funcionamento como utilizador de raiz devem ser evitados
- A transferência segura para contas de armazenamento deve ser ativada
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign
- Os clusters de tecido de serviço só devem utilizar o Azure Ative Directy para a autenticação do cliente
- Os serviços devem ouvir apenas os portos permitidos
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure
- As contas de armazenamento devem restringir o acesso à rede usando regras de rede virtuais
- A utilização da rede de acolhimento e das portas deve ser restringida
- A utilização dos suportes de volume do Pod HostPath deve ser restringida a uma lista conhecida para restringir o acesso do nó a partir de contentores comprometidos
- O período de validade dos certificados armazenados no Cofre da Chave Azure não deve exceder 12 meses
- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager
- Firewall de aplicação web (WAF) deve ser ativado para Gateway de aplicação
- Firewall de aplicação web (WAF) deve ser ativado para o serviço de porta frontal Azure

Estas recomendações podem ser utilizadas com a opção **de aplicação:**

- A auditoria no servidor SQL deve ser ativada
- A azure Backup deve ser ativado para máquinas virtuais
- O Azure Defender para SQL deve ser ativado nos seus servidores SQL
- Os registos de diagnóstico no Azure Stream Analytics devem ser ativados
- Os registos de diagnóstico nas contas do Lote devem ser ativados
- Os registos de diagnóstico em Data Lake Analytics devem ser ativados
- Os registos de diagnóstico no Centro de Eventos devem ser ativados
- Os registos de diagnóstico no Cofre de Chaves devem ser ativados
- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados
- Os registos de diagnóstico nos serviços de pesquisa devem ser ativados
- Os registos de diagnóstico no Service Bus devem ser ativados