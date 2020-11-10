---
title: Azure Security Benchmark V2 - Proteção de Dados
description: Proteção de dados V2 de referência de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bde95006865a2aab2da4a5d6d27ccccc14d74d12
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408927"
---
# <a name="security-control-v2-data-protection"></a>Controlo de Segurança V2: Proteção de Dados

A Proteção de Dados cobre o controlo da proteção de dados em repouso, em trânsito e através de mecanismos de acesso autorizados. Isto inclui descobrir, classificar, proteger e monitorizar ativos de dados sensíveis usando controlo de acesso, encriptação e login no Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Descoberta, classificação e rotulagem de dados sensíveis

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-1 | 13.1, 14.5, 14.7 | SC-28 |

Descubra, classifique e rotule os seus dados sensíveis para que possa conceber os controlos apropriados para garantir que informações sensíveis são armazenadas, processadas e transmitidas de forma segura pelos sistemas tecnológicos da organização. 

Utilize a Azure Information Protection (e a sua ferramenta de digitalização associada) para obter informações confidenciais dentro de documentos do Office sobre Azure, no local, no Office 365 e em outros locais. 

Pode utilizar a Azure SQL Information Protection para ajudar na classificação e rotulagem das informações armazenadas nas Bases de Dados Azure SQL.

- [Etiquetar informações sensíveis usando a Proteção de Informações Azure](/azure/information-protection/what-is-information-protection) 

- [Como implementar a Azure SQL Data Discovery](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilidade** : Compartilhado

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados sensíveis

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-2 | 13.2, 2.10 | SC-7, AC-4 |

Proteja os dados sensíveis restringindo o acesso utilizando o Azure Role Based Access Control (Azure RBAC), controlos de acesso baseados em rede e controlos específicos nos serviços Azure (como encriptação em SQL e outras bases de dados). 

Para garantir um controlo de acesso consistente, todos os tipos de controlo de acesso devem estar alinhados com a sua estratégia de segmentação empresarial. A estratégia de segmentação da empresa também deve ser informada pela localização de dados e sistemas críticos sensíveis ou empresariais.

Para a plataforma subjacente, que é gerida pela Microsoft, a Microsoft trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou alguns controlos e capacidades de proteção de dados predefinidos.

- [Controlo de Acesso Baseado em Função Azure (RBAC)](../../role-based-access-control/overview.md)

- [Compreender a proteção de dados do cliente em Azure](../fundamentals/protection-customer-data.md)

**Responsabilidade** : Compartilhado

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitor para transferência não autorizada de dados sensíveis

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-3 | 13.3 | AC-4, SI-4 |

Monitorize para transferência não autorizada de dados para locais fora da visibilidade e controlo da empresa. Isto normalmente envolve monitorização para atividades anómalas (transferências grandes ou incomuns) que podem indicar exfiltração de dados não autorizados. 

A Azure Storage Advanced Threat Protection (ATP) e Azure SQL ATP podem alertar para a transferência anómala de informações que possam indicar transferências não autorizadas de informações sensíveis. 

A Azure Information Protection (AIP) fornece capacidades de monitorização para informações classificadas e rotuladas. 

Se necessário para o cumprimento da prevenção de perda de dados (DLP), pode utilizar uma solução DLP baseada no hospedeiro para impor controlos detetives e/ou preventivos para evitar a exfiltração de dados.

- [Ativar Azure SQL ATP](../../azure-sql/database/threat-detection-overview.md)

- [Ativar o Azure Storage ATP](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Responsabilidade** : Compartilhado

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Segurança de aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Criptografar informações sensíveis em trânsito

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

Para complementar os controlos de acesso, os dados em trânsito devem ser protegidos contra ataques "fora de banda" (por exemplo, captura de tráfego) utilizando encriptação para garantir que os atacantes não podem ler ou modificar facilmente os dados. 

Embora isto seja facultativo para o tráfego em redes privadas, isto é fundamental para o tráfego em redes externas e públicas. Para o tráfego HTTP, certifique-se de que qualquer cliente que se conecte aos seus recursos Azure pode negociar TLS v1.2 ou maior. Para uma gestão remota, utilize SSH (para Linux) ou RDP/TLS (para Windows) em vez de um protocolo não encriptado. As versões e protocolos obsoletos de SSL, TLS e SSH, e as cifras fracas devem ser desativadas.  

Por padrão, o Azure fornece encriptação para dados em trânsito entre centros de dados Azure. 

- [Compreender a encriptação em trânsito com Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informação sobre Segurança TLS](/security/engineering/solving-tls1-problem)

- [Dupla encriptação para dados do Azure em trânsito](../fundamentals/double-encryption.md#data-in-transit)

**Responsabilidade** : Compartilhado

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Criptografe dados sensíveis em repouso

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-5 | 14.8 | SC-28, SC-12 |

Para complementar os controlos de acesso, os dados em repouso devem ser protegidos contra ataques "fora da banda" (como aceder ao armazenamento subjacente) utilizando encriptação. Isto ajuda a garantir que os atacantes não podem ler ou modificar facilmente os dados. 

O Azure fornece encriptação para dados em repouso por padrão. Para dados altamente sensíveis, você tem opções para implementar encriptação adicional em repouso em todos os recursos Azure, sempre que disponível. O Azure gere as suas chaves de encriptação por padrão, mas o Azure oferece opções para gerir as suas próprias chaves (chaves geridas pelo cliente) para certos serviços Azure.

- [Compreenda a encriptação em repouso em Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Como configurar chaves de encriptação geridas pelo cliente](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Modelo de encriptação e tabela de gestão chave](../fundamentals/encryption-models.md)

- [Dados em repouso dupla encriptação em Azure](../fundamentals/double-encryption.md#data-at-rest)

**Responsabilidade** : Compartilhado

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)