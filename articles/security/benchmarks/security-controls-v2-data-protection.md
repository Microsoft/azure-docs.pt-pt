---
title: Azure Security Benchmark V2 - Proteção de Dados
description: Proteção de dados V2 de referência de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c8d907062835f18393946b04f1f1e9d5ec345411
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735765"
---
# <a name="security-control-v2-data-protection"></a>Controlo de Segurança V2: Proteção de Dados

A Proteção de Dados cobre o controlo da proteção de dados em repouso, em trânsito e através de mecanismos de acesso autorizados. Isto inclui descobrir, classificar, proteger e monitorizar ativos de dados sensíveis usando controlo de acesso, encriptação e login no Azure.

Para ver a política de Azure incorporada aplicável, consulte [detalhes da iniciativa de conformidade regulamentar de referência de segurança Azure: Proteção de dados](../../governance/policy/samples/azure-security-benchmark#data-protection)

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Descobrir, classificar e etiquetar dados confidenciais

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| DP-1 | 13.1, 14.5, 14.7 | SC-28 |

Descubra, classifique e rotule os seus dados sensíveis para que possa conceber os controlos apropriados para garantir que informações sensíveis são armazenadas, processadas e transmitidas de forma segura pelos sistemas tecnológicos da organização.

Utilize o Azure Information Protection (e a ferramenta de análise associada do mesmo) para informações confidenciais em documentos do Office no Azure, em ambientes no local, no Office 365 e noutras localizações.

Pode utilizar o Azure SQL Information Protection para ajudar na classificação e etiquetagem das informações armazenadas nas bases de dados da Base de Dados SQL do Azure.

- [Etiquetar informações confidenciais com o Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Como implementar a Descoberta de Dados do SQL do Azure](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilidade**: Partilhada

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados confidenciais

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| DP-2 | 13.2, 2.10 | SC-7, AC-4 |

Proteja os dados sensíveis restringindo o acesso utilizando o controlo de acesso baseado em funções Azure (Azure RBAC), controlos de acesso baseados em rede e controlos específicos nos serviços Azure (como encriptação em SQL e outras bases de dados). 

Para garantir um controlo de acesso consistente, todos os tipos de controlo de acesso devem estar alinhados com a estratégia de segmentação da sua empresa. A estratégia de segmentação da empresa também deve ter conhecimento da localização dos dados confidenciais e dos sistemas críticos para a empresa.

Quanto à plataforma subjacente, que é gerida pela Microsoft, a Microsoft encara todos os conteúdos dos clientes como confidenciais e protege-os da perda e exposição dos dados de cliente. Para assegurar que os dados permanecem seguros no Azure, a Microsoft implementou alguns controlos e capacidades de proteção de dados predefinidos.

- [Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/overview.md)

- [Compreender a proteção dos dados dos clientes no Azure](../fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorizar a transferência não autorizada de dados confidenciais

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| DP-3 | 13.3 | AC-4, SI-4 |

Monitorize para transferência não autorizada de dados para locais fora da visibilidade e controlo da empresa. Tipicamente, esta orientação envolve a monitorização de atividades anómalas (transferências grandes ou incomuns) que podem indicar a exfiltração não autorizada de dados. 

A Proteção Avançada Contra Ameaças (ATP) do Azure e o ATP do SQL do Azure podem alertar para transferências anómalas de informações, as quais podem indicar transferências não autorizadas de informações confidenciais. 

O Azure Information Protection (AIP) proporciona capacidades de monitorização de informações que tenham sido classificadas e etiquetadas. 

Se necessário para a conformidade em termos da prevenção contra perda de dados (Data Loss Prevention, DLP), pode utilizar uma solução de DLP baseada em anfitrião para impor controlos de deteção e/ou prevenção para evitar a exfiltração de dados.

- [Azure Defender para SQL](../../azure-sql/database/azure-defender-for-sql.md)

- [Azure Defender para Armazenamento](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Responsabilidade**: Partilhada

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Segurança de aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Encriptar informações confidenciais em trânsito

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Para complementar os controlos de acesso, os dados em trânsito devem ser protegidos contra ataques "fora de banda" (como a captura de tráfego) utilizando encriptação para garantir que os atacantes não podem ler ou modificar facilmente os dados.

Embora isto seja facultativo para o tráfego em redes privadas, isto é fundamental para o tráfego em redes externas e públicas. Para o tráfego HTTP, certifique-se de que qualquer cliente que se conecte aos seus recursos Azure pode negociar TLS v1.2 ou maior. Para uma gestão remota, utilize SSH (para Linux) ou RDP/TLS (para Windows) em vez de um protocolo não encriptado. As versões e protocolos obsoletos de SSL, TLS e SSH, e as cifras fracas devem ser desativadas.

Por padrão, o Azure fornece encriptação para dados em trânsito entre centros de dados Azure.

- [Compreender a encriptação em trânsito com Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informações sobre a Segurança TLS](/security/engineering/solving-tls1-problem)

- [Dupla encriptação para dados do Azure em trânsito](../fundamentals/double-encryption.md#data-in-transit)

**Responsabilidade**: Partilhada

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Encripte os dados confidenciais inativos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

Para complementar os controlos de acesso, os dados em repouso devem ser protegidos contra ataques "fora da banda" (como aceder ao armazenamento subjacente) utilizando encriptação. Isto ajuda a garantir que os atacantes não podem ler ou modificar facilmente os dados. 

O Azure fornece encriptação para dados em repouso por padrão. Para dados altamente sensíveis, você tem opções para implementar encriptação adicional em repouso em todos os recursos Azure, sempre que disponível. O Azure gere as suas chaves de encriptação por padrão, mas o Azure oferece opções para gerir as suas próprias chaves (chaves geridas pelo cliente) para certos serviços Azure.

- [Compreender a encriptação de dados inativos no Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Como configurar chaves de encriptação geridas pelo cliente](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Modelo de encriptação e tabela de gestão chave](../fundamentals/encryption-models.md)

- [Dados em repouso dupla encriptação em Azure](../fundamentals/double-encryption.md#data-at-rest)

**Responsabilidade**: Partilhada

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)