---
title: Requisitos de certificação edge secured-core
description: Requisitos do programa de certificação edge secured-core
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166909"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Dispositivo Certificado Azure - Edge Secured-core (Pré-visualização) #

## <a name="edge-secured-core-certification-requirements"></a>Requisitos de certificação edge Secured-Core ##

Este documento descreve as capacidades e requisitos específicos do dispositivo que serão cumpridos de forma a completar a certificação e listar um dispositivo no catálogo do Dispositivo Azure IoT com a etiqueta edge secured-core.

### <a name="program-purpose"></a>Finalidade do Programa ###
Edge Secured-core é uma certificação incremental no programa Azure Certified Device para dispositivos IoT que executam um sistema operativo completo, como o Linux ou o Windows 10 IoT.Este programa permite aos parceiros de dispositivos diferenciar os seus dispositivos cumprindo um conjunto adicional de critérios de segurança. Os dispositivos que satisfazem estes critérios permitem estas promessas:
1. Identidade do dispositivo baseada em hardware 
2. Capaz de impor a integridade do sistema 
3. Mantém-se atualizado e é remotamente manejável
4. Fornece proteção de dados no descanso
5. Fornece proteção de dados em trânsito
6. Construído em agente de segurança e endurecimento
### <a name="requirements"></a>Requisitos ###

---
|Name|SecuredCore.Built-in.Security|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é garantir que os dispositivos podem reportar informações e eventos de segurança enviando dados para a Azure Defender para IoT.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação |O dispositivo deve gerar registos de segurança e alertas. Registos de dispositivos e alerta mensagens para o Centro de Segurança Azure.<ol><li>Descarregue e implemente o agente de segurança do GitHub</li><li>Valide a mensagem de alerta do Azure Defender para IoT.</li></ol>|
|Recursos|[Azure Docs IoT Defender para IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Name|SecuredCore.Encryption.Storage|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste para validar esses dados sensíveis pode ser encriptado em armazenamento não volátil.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para garantir que a encriptação de armazenamento está ativada e o algoritmo padrão é XTS-AES, com comprimento de chave de 128 bits ou superior.|
|Recursos||

---
|Name|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|Estado|Opcional|
|Description|O objetivo do teste para validar a existência de um enclave seguro e que o enclave esteja acessível a partir de um agente seguro.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para garantir que o Agente de Segurança Azure possa comunicar com o enclave seguro|
|Recursos|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Name|SecuredCore.Hardware.Identidade|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar a identificação do dispositivo está enraizada no hardware.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para garantir que o dispositivo tem um TPM presente e que pode ser a provisionado através do IoT Hub utilizando a chave de endosso TPM.|
|Recursos|[Configuração de provisão automática com DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Name|SecuredCore.Update|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar o dispositivo pode receber e atualizar o seu firmware e software.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|A confirmação do parceiro de que foram capazes de enviar uma atualização para o dispositivo através da atualização da Microsoft, atualização do Dispositivo Azure ou outros serviços aprovados.|
|Recursos|[Atualização do dispositivo para ioT hub](../iot-hub-device-update/index.yml)|

---
|Name|SecuredCore.Manageability.Configuration|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar os dispositivos que suportam a gestão de segurança remota.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|O dispositivo a ser validado através de ferramentas para garantir que o dispositivo suporta a capacidade de ser remotamente manejável e especificamente configurações de segurança. E o estado é reportado de volta ao IoT Hub/Azure Defender para ioT.|
|Recursos||

---
|Name|SecuredCore.Manageability.Reset|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo deste teste é validar o dispositivo contra dois casos de utilização: a) Capacidade de efetuar um reset (remover dados do utilizador, remover configs do utilizador), b) Restaurar o dispositivo para ser bem conhecido pela última vez no caso de uma atualização que cause problemas.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de uma combinação de conjuntos de ferramentas e documentação submetida que o dispositivo suporta esta funcionalidade. O fabricante do dispositivo pode determinar se implementa estas capacidades para suportar o reset remoto ou apenas o reset local.|
|Recursos||

---
|Name|SecuredCore.Updates.Duração|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo desta política é garantir que o dispositivo se mantenha seguro.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual|
|Validação|O compromisso da submissão de dispositivos certificados será necessário para manter os dispositivos atualizados durante 60 meses a partir da data de submissão. As especificações disponíveis para o comprador e os próprios dispositivos de alguma forma devem indicar a duração para a qual o seu software será atualizado.|
|Recursos||

---
|Name|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo desta política é garantir que existe um mecanismo de recolha e distribuição de relatórios de vulnerabilidades no produto.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual|
|Validação|A documentação sobre o processo de envio e receção de relatórios de vulnerabilidade para os dispositivos certificados será revista.|
|Recursos||

---
|Name|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo desta política é garantir que as vulnerabilidades que são elevadas/críticas (utilizando o CVSS 3.0) sejam abordadas no prazo de 180 dias após a disponibilização da correção.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual|
|Validação|A documentação sobre o processo de envio e receção de relatórios de vulnerabilidade para os dispositivos certificados será revista.|
|Recursos||


---
|Name|SecuredCore.Encryption.TLS|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar o suporte para versões TLS e suítes de cifra necessárias.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
Validação|Dispositivo a ser validado através de conjunto de ferramentas para garantir que o dispositivo suporta uma versão TLS mínima de 1.2 e suporta as seguintes suites de cifra TLS necessárias.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Recursos| [Suporte TLS no IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [Suítes TLS Cipher no Windows 10](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Name|SecuredCore.Protection.SignedUpdates|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar que as atualizações devem ser assinadas.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para garantir que as atualizações ao sistema operativo, controladores, software de aplicação, bibliotecas, pacotes e firmware não serão aplicadas a menos que devidamente assinadas e validadas.
|Recursos||

---
|Name|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar a integridade do arranque do dispositivo.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para garantir que as assinaturas de firmware e kernel são validadas sempre que o dispositivo bota. <ul><li>UEFI: O arranque seguro está ativado</li><li>Uboot: A bota verificada está ativada</li></ul>|
|Recursos||

---
|Name|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo deste teste é validar que a integridade do código está disponível neste dispositivo.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para garantir que a integridade do código está ativada. </br> Janelas: HVCI </br> Linux: dm-verity e IMA|
|Recursos||

---
|Name|SecuredCore.Protection.NetworkServices|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar que as aplicações que aceitam a entrada da rede não estão a funcionar com privilégios elevados.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para garantir que os serviços que aceitam ligações de rede não estão a funcionar com sistema ou privilégios de raiz.|
|Recursos||

---
|Name|SecuredCore.Protection.Baselines|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar que o sistema está em conformidade com uma configuração de segurança de base.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para garantir que as configurações do sistema Defender IOT foram executadas.|
|Recursos| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Name|SecuredCore.Firmware.Protection|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é garantir que o dispositivo tenha mitigações adequadas das ameaças à segurança do Firmware.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de ferramentas para confirmar que está protegido contra ameaças à segurança do firmware através de uma das seguintes abordagens: <ul><li>DrtM + Mitigação do Modo de Gestão DA UEFI</li><li>Endurecimento do modo de gestão DRTM + UEFI</li><li>FW aprovado que faz endurecimento de firmware SRTM + tempo de execução</li></ul> |
|Recursos| https://trustedcomputinggroup.org/ |

---
|Name|SecuredCore.Firmware.Attestation|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é garantir que o dispositivo pode atestar remotamente o serviço Microsoft Azure Atesstation.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Dispositivo a ser validado através de conjunto de ferramentas para garantir que os registos de arranque da plataforma e as medições da atividade do arranque podem ser recolhidos e atestado remotamente ao serviço microsoft Azure Attestation.|
|Recursos| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|Name|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do ensaio é validar que o DMA não está ativado em portas acessíveis externamente.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|Se existirem portas externas capazes de DMA no dispositivo, o instrumento para validar a IOMMU ou a SMMU está ativado e configurado para essas portas.|
|Recursos||

---
|Name|SecuredCore.Protection.Debug|
|:---|:---|
|Estado|Obrigatório|
|Descrição|O objetivo do teste é validar que a funcionalidade de depuração no dispositivo está desativada.|
|Disponibilidade de destino|2021|
|Aplica-se A|Qualquer dispositivo|
|SO|Desconhecida|
|Tipo de Validação|Manual/Ferramentas|
|Validação|O dispositivo a ser validado através de ferramentas para garantir que a funcionalidade de depuragem requer autorização para ativar.|
|Recursos||
