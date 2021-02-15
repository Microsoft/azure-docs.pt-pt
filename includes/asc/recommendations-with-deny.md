---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 02/09/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: b5d33a962078ab7bd49c58fe16f271cdd1c8eea4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379200"
---
- O acesso a contas de armazenamento com firewall e configurações de rede virtuais deve ser restringido
- As variáveis de conta de automação devem ser encriptadas
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
- O acesso público à conta de armazenamento deve ser proibido
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure
- As contas de armazenamento devem restringir o acesso à rede usando regras de rede virtuais
- A utilização da rede de acolhimento e das portas deve ser restringida
- A utilização dos suportes de volume do Pod HostPath deve ser restringida a uma lista conhecida para restringir o acesso do nó a partir de contentores comprometidos
- O período de validade dos certificados armazenados no Cofre da Chave Azure não deve exceder 12 meses
- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager
- Firewall de aplicação web (WAF) deve ser ativado para Gateway de aplicação
- Firewall de aplicação web (WAF) deve ser ativado para o serviço de porta frontal Azure

