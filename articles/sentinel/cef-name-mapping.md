---
title: Chave de formato de evento comum (CEF) e mapeamento de campo CommonSecurityLog
description: Este artigo mapeia as chaves CEF dos nomes de campo correspondentes no CommonSecurityLog em Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 04/12/2021
ms.openlocfilehash: 1670d1bb291e30295018146f2a24c5282feac6e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311656"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>Mapeamento de campo CEF e CommonSecurityLog

As tabelas que se seguem mapeiam nomes de campo do Formato Comum de Eventos (CEF) para os nomes que utilizam no CommonSecurityLog do Azure Sentinel, e podem ser úteis quando estiver a trabalhar com uma fonte de dados CEF em Azure Sentinel.

Para obter mais informações, consulte [Conecte a sua solução externa utilizando o Formato Comum de Eventos.](connect-common-event-format.md)

## <a name="a---c"></a>A - C

|Nome chave CEF  |Nome de campo CommonSecurityLog  |Description  |
|---------|---------|---------|
| agir    |    <a name="deviceaction"></a> Aacção de Dispositivos     |  A ação mencionada no evento.       |
|   aplicação  |    ApplicationProtocol     |  O protocolo utilizado na aplicação, tais como HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAPS, etc.   |
| cnt    |    Conta de Eventos     |  Uma contagem associada ao evento, mostrando quantas vezes o mesmo evento foi observado.       |
| | | |

## <a name="d"></a>D

|Nome chave CEF  |Nome CommonSecurityLog  |Description  |
|---------|---------|---------|
|Fornecedor de dispositivos     |  DispositivoVendor       | String que, juntamente com as definições de produto e versão do dispositivo, identifica exclusivamente o tipo de dispositivo de envio.       |
|Produto do dispositivo     |   Produto de dispositivos      |   String que, juntamente com as definições de fornecedor de dispositivos e versão, identifica exclusivamente o tipo de dispositivo de envio.        |
|Versão do dispositivo     |   Versão de Dispositivos      |      String que, juntamente com as definições de produto do dispositivo e fornecedor, identifica exclusivamente o tipo de dispositivo de envio.     |
| destinoDnsDomain    | DestinationDnsDomain        |   A parte DNS do nome de domínio totalmente qualificado (FQDN).      |
| nome de serviço de destino | Nome do DestinationService | O serviço que é alvo do evento. Por exemplo, `sshd`.|
| destinoTranslatedAddress | DestinoTranslatadoAddress | Identifica o destino traduzido referido pelo evento numa rede IP, como um endereço IP IPv4. |
| destinoTranslatedPort | DestinationTranslatedPort | Porto, após tradução, como uma firewall. <br>Números de porta válidos: `0` - `65535` |
| direção de dispositivos | <a name="communicationdirection"></a> Direção de Comunicação | Qualquer informação sobre o rumo que a comunicação observada tomou. Valores válidos: <br>- `0` = Entrada <br>- `1` = Saída |
| deviceDnsDomain | DeviceDnsDomain | A parte do domínio DNS do nome de domínio completo qualificado (FQDN) |
|DeviceEventClassID     |   DeviceEventClassID     |   String ou inteiro que serve como um identificador único por tipo de evento.      |
| dispositivoExternalID | DeviceExternalID | Um nome que identifica exclusivamente o dispositivo que gera o evento. |
| dispositivoSicilidade | DispositivoFacilidade | As instalações que geram o evento.|
| dispositivoInboundInterface | DispositivoInboundInterface |A interface na qual o pacote ou dados introduziram o dispositivo.  |
| dispositivoNtDomain | DeviceNtDomain | O domínio Windows do endereço do dispositivo |
| dispositivoOutboundInterface | DispositivoOutboundInterface |Interface na qual o pacote ou dados deixaram o dispositivo. |
| devicePayloadId |DevicePayloadId |Identificador único para a carga útil associada ao evento. |
| nome de processamento de dispositivos | ProcessName | Nome do processo associado ao evento. <br><br>Por exemplo, na UNIX, o processo que gera a entrada de syslog. |
| dispositivoTranslatedAddress | DispositivoTranslatedAddress | Identifica o endereço do dispositivo traduzido a que o evento se refere, numa rede IP. <br><br>O formato é um endereço Ipv4. |
| dhost |Nome DestinationHost | O destino a que o evento se refere numa rede IP.  <br>O formato deve ser um FQDN associado ao nó de destino, quando um nó está disponível. Por exemplo, `host.domain.com` ou `host`. |
| dmac | DestinationMacAddress | O endereço MAC de destino (FQDN) |
| dntdom | DestinationNTDomain | O nome de domínio do Windows do endereço de destino.|
| dpid | DestinationProcessId |O ID do processo de destino associado ao evento.|
| dpriv | DestinationUserPrivileges | Define os privilégios de utilização do destino. <br>Valores válidos: `Admninistrator` `User` , `Guest` |
| dproc | Nome de DestinoProcessa | O nome do processo de destino do evento, como `telnetd` ou `sshd.` |
| dpt | DestinationPort | Porto de destino. <br>Valores válidos: `*0` - `65535` |
| dst | DestinationIP | O endereço IpV4 de destino a que o evento se refere numa rede IP. |
| dtz | DeviceTimeZon | Timezone do dispositivo que gera o evento |
| duid |DestinationUserId | Identifica o utilizador de destino por ID. |
| duser | Nome de DestinoUser |Identifica o utilizador de destino pelo nome.|
| dvc | DispositivoAddress | O endereço IPv4 do dispositivo que gera o evento. |
| dvchost | DeviceName | O FQDN associado ao nó do dispositivo, quando um nó está disponível. Por exemplo, `host.domain.com` ou `host`.| 
| dvcmac | DeviceMacAddress | O endereço MAC do dispositivo que gera o evento. |
| dvcpid | ID de Processo | Define o ID do processo no dispositivo que gera o evento. |

## <a name="e---i"></a>E - I

|Nome chave CEF  |Nome CommonSecurityLog  |Description  |
|---------|---------|---------|
|fim     |  EndTime       | Terminou o momento em que a atividade relacionada com o evento terminou.        |
|externalId    |   EXTERNALID      | Um ID usado pelo dispositivo de origem. Tipicamente, estes valores têm valores crescentes que estão cada um associado a um evento.        |
|fileCreateTime     |  Hora do Arquivo      | Hora em que o ficheiro foi criado.        |
|fileHash     |   Hash de Ficheiro      |   Haxixe de um ficheiro.      |
|fileId     |   FileID      |Um ID associado a um ficheiro, como o inode.         |
| fileModificaçãoTime | Tempo de Arquivo |Hora em que o ficheiro foi modificado pela última vez. |
| filePath | FilePath | Caminho completo para o ficheiro, incluindo o nome de arquivo. Por exemplo: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` ou `/usr/bin/zip` . .|
| arquivoPermission |FilePermission |As permissões do ficheiro. |
| fileType | FileType | Tipo de ficheiro, como tubo, tomada, e assim por diante.|
|fname | Nome de arquivo| O nome do ficheiro, sem o caminho. |
| fsize | Tamanho de arquivo | O tamanho do ficheiro. |
|Anfitrião    |  Computador       | Anfitrião, de Syslog        |
|para dentro     |  ReceivedBytes      |Número de bytes transferidos para a entrada.         |
| | | |

## <a name="m---p"></a>M - P

|Nome chave CEF  |Nome CommonSecurityLog  |Description  |
|---------|---------|---------|
|msg   |  Mensagem       | Uma mensagem que dá mais detalhes sobre o evento.        |
|Name     |  Atividade       |   Uma corda que representa uma descrição legível e compreensível do evento.     |
|OldFileCreateTime     |  OldFileCreateTime       | Tempo quando o ficheiro antigo foi criado.        |
|OldFileHash     |   OldFileHash      |   Haxixe do ficheiro antigo.      |
|OldFileId     |   OldFileId     |   E identificação associada ao ficheiro antigo, como o inode.      |
| OldFileModificationTime | Tempo de AntigaModificação |Hora em que o ficheiro antigo foi modificado pela última vez. |
| oldFileName |  Nome oldFile |O nome do ficheiro antigo. |
| OldFilePath | OldFilePath | Caminho completo para o ficheiro antigo, incluindo o nome de arquivo. <br>Por exemplo, `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` ou `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |Permissões do antigo ficheiro. |
|oldFileSize | OldFileSize | Tamanho do ficheiro antigo.|
| OldFileType | OldFileType | Tipo de arquivo do ficheiro antigo, como um tubo, tomada, e assim por diante.|
| para fora | SentBytes | Número de bytes transferidos para fora. |
| Resultado | Resultado | Resultado do evento, tais `success` `failure` como.|
|proto    |  Protocolo       | Protocolo de transporte que identifica o protocolo Camada 4 usado. <br><br>Os valores possíveis incluem nomes de protocolos, tais `TCP` `UDP` como.        |
| | | |

## <a name="r---t"></a>R - T

|Nome chave CEF  |Nome CommonSecurityLog  |Description  |
|---------|---------|---------|
|Razão     |  Razão      |A razão pela qual um evento de auditoria foi gerado. <br><br>Por exemplo, `Bad password` ou `Unknown user`.         |
|Pedir     |   RequestURL      | O URL acedeu a um pedido HTTP, incluindo o protocolo. Por exemplo, `http://www/secure.com`        |
|solicitaçãoClientApplicação     |   PedidoClientApplicação      |   O agente utilizador associado ao pedido.      |
| pedidoContexto | PedidoContexto | Descreve o conteúdo de onde o pedido teve origem, tal como o remetente HTTP. |
| solicitaçãoCookies | RequestCookies |Cookies associados ao pedido. |
| requestMethod | RequestMethod | O método usado para aceder a uma URL. <br><br>Valores válidos incluem métodos `POST` `GET` como, e assim por diante. |
| rt | Hora do Recibo | Foi recebida a hora em que o evento relacionado com a atividade foi recebido. |
|Gravidade     |  <a name="logseverity"></a> LogSeverity       |  Uma corda ou inteiro que descreve a importância do evento.<br><br> Valores de cordas válidos: `Unknown` `Low` , , `Medium` , `High``Very-High` <br><br>Os valores inteiros válidos são:<br> - `0`-`3` = Baixo <br>- `4`-`6` = Médio<br>- `7`-`8` = Alto<br>- `9`-`10` = Very-High |
| shost    | FonteHostName        |Identifica a fonte a que o evento se refere numa rede IP. O formato deve ser um nome de domínio totalmente qualificado (DQDN) associado ao nó de origem, quando um nó estiver disponível. Por exemplo, `host` ou `host.domain.com`. |
| smac | SourceMacAddress | Endereço MAC de origem. |
| sntdom | SourceNTDomain | O nome de domínio do Windows para o endereço de origem. |
| fonteDnsDomain | SourceDnsDomain | O domínio DNS parte da FQDN completa. |
| nome sourceService | Nome SourceService | O serviço responsável pela geração do evento. |
| fonteTranslatedAddress | FonteTranslatadoAddress | Identifica a fonte traduzida a que o evento se refere numa rede IP. |
| fonteTranslatedPort | FonteTranslatedPort | Fonte porta após tradução, como uma firewall. <br>Os números de porta válidos `0`  -  `65535` são. |
| espiado | SourceProcessId | A identificação do processo de origem associado ao evento.|
| spriv | SourceUserPrivileges | Os privilégios do utilizador de origem. <br><br>Valores válidos incluem: `Administrator` `User` , `Guest` |
| sproc | Nome Origem Processo | O nome do processo de origem do evento.|
| spt | FontePort | O número da porta de origem. <br>Os números de porta válidos `0`  -  `65535` são. |
| src | SourceIP |A fonte a que um evento se refere numa rede IP, como um endereço IPv4. |
| iniciar | StartTime | O momento em que a atividade a que o evento se refere começou. |
| suid | SourceUserID | Identifica o utilizador de origem por ID. |
| suser | Nome sourceUser | Identifica o utilizador de origem pelo nome. |
| tipo | EventType | Tipo de evento. Os valores de valor incluem: <br>- `0`: evento base <br>- `1`: agregado <br>- `2`: evento de correlação <br>- `3`: evento de ação <br><br>**Nota:** Este evento pode ser omitido para eventos de base. |
| | | |

## <a name="custom-fields"></a>Campos personalizados

As tabelas que se seguem mapeiam os nomes das chaves CEF e dos campos CommonSecurityLog que estão disponíveis para os clientes utilizarem dados que não se aplicam a nenhum dos campos incorporados.

### <a name="custom-ipv6-address-fields"></a>Campos de endereços IPv6 personalizados

Os seguintes mapas de tabelas cef e nomes CommonSecurityLog para os campos de *endereços IPv6* disponíveis para dados personalizados.

|Nome chave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     c6a1    |     DispositivoSTomtomiPv6Address1       |
|     c6a1Label    |     DispositivoSTomtomiPv6Address1Label    |
|     c6a2    |     DispositivoSTomópv6Address2    |
|     c6a2Label    |     DispositivoSTomôpv6Address2Label    |
|     c6a3    |     DispositivoSTomóPv6Address3    |
|     c6a3Label    |     DispositivoSTomóPv6Address3Label    |
|     c6a4    |     DispositivoSTomóPv6Address4    |
|     c6a4Label    |     DispositivoSTomtomiPv6Address4Label    |
|     cfp1    |     DispositivoSCustomFloatingPoint1    |
|     cfp1Label    |     dispositivoStomFloatingPoint1Label    |
|     cfp2    |     DispositivoSCustomFloatingPoint2    |
|     cfp2Label    |     dispositivoStomFloatingPoint2Label    |
|     cfp3    |     DispositivoSCustomFloatingPoint3    |
|     cfp3Label    |     dispositivoStomFloatingPoint3Label    |
|     cfp4    |     DispositivoSCustomFloatingPoint4    |
|     cfp4Label    |     dispositivoSTomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>Campos de números personalizados

Os seguintes mapas de tabelas cef e nomes CommonSecurityLog para os campos *de números* disponíveis para dados personalizados.

|Nome chave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     cn1    |     DispositivoSCustomNumber1       |
|     cn1Label    |     DispositivoSCustomNumber1Label       |
|     cn2    |     DispositivoSCustomNumber2       |
|     cn2Label    |     DispositivoSTomNumber2Label       |
|     cn3    |     DispositivoCustomNumber3       |
|     cn3Label    |     DispositivoSCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>Campos de cordas personalizados

Os seguintes mapas de tabelas cef e nomes CommonSecurityLog para os campos de *cordas* disponíveis para dados personalizados.

|Nome chave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     cs1    |     DispositivoSScentramento 1 <sup> [1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup> [1](#use-sparingly)</sup>    |
|     cs2    |     DispositivoSStoStramento2 <sup> [1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label <sup> [1](#use-sparingly)</sup> |
|     cs3    |     DispositivoSStoStramento3 <sup> [1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label <sup> [1](#use-sparingly)</sup> |
|     cs4    |     DispositivoSScentring4 <sup> [1](#use-sparingly)</sup> |
|     cs4Label    |     DispositivoSTomString4Label <sup> [1](#use-sparingly)</sup>  |
|     cs5    |     DispositivoSStoStramento5 <sup> [1](#use-sparingly)</sup>   |
|     cs5Label    |     DispositivoSTomString5Label <sup> [1](#use-sparingly)</sup>    |
|     cs6    |     DispositivoSStoStramento6 <sup> [1](#use-sparingly)</sup> |
|     cs6Label    |     DispositivoSTomString6Label <sup> [1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> Recomendamos que utilize os campos **DeviceCustomString** com moderação e utilize campos mais específicos e incorporados sempre que possível.
> 

### <a name="custom-timestamp-fields"></a>Campos de hora com tempos personalizados

Os seguintes mapas de tabelas cef e nomes CommonSecurityLog para os campos *de marcação de tempo* disponíveis para dados personalizados.

|Nome chave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     dispositivoStomDate1    |     DispositivoSTomDate1    |
|     dispositivoCustomDate1Label    |     DispositivoCustomDate1Label    |
|     dispositivoStomDate2       |     DispositivoSTomDate2    |
|     dispositivoCustomDate2Label    |     DispositivoCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>Campos de dados inteiros personalizados

Os seguintes mapas de tabelas cef e nomes CommonSecurityLog para os campos *inteiros* disponíveis para dados personalizados.

|Nome chave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>Campos de enriquecimento

Os seguintes campos **CommonSecurityLog** são adicionados pelo Azure Sentinel para enriquecer os eventos originais recebidos dos dispositivos de origem, e não têm mapeamentos em teclas CEF:

### <a name="threat-intelligence-fields"></a>Campos de inteligência de ameaça

|Nome de campo CommonSecurityLog  |Description  |
|---------|---------|
|   **IndicadorThreatType**  |  O tipo de ameaça [Maliciosa,](#MaliciousIP) de acordo com o feed de inteligência de ameaça.       |
| <a name="MaliciousIP"></a>**Ip malicioso** | Lista quaisquer endereços IP na mensagem que se correlaciona com o feed de inteligência de ameaça atual. |
|  **Países maliciosos doIP**   | O país [MaliciousIP,](#MaliciousIP) de acordo com a informação geográfica no momento da ingestão de registos.        |
| **MaliciosaIPAtitude**    |   A [longitude Maliciosa,](#MaliciousIP) de acordo com a informação geográfica no momento da ingestão de registos.      |
| **IpLongitude maliciosa**    |  A [longitude Maliciosa,](#MaliciousIP) de acordo com a informação geográfica no momento da ingestão de registos.       |
| **ReportReferenceLink**    |    Ligação com o relatório da inteligência de ameaça.     |
|  **Confidência ameaçadora**   |   A confiança da ameaça [maliciosa,](#MaliciousIP) de acordo com o feed da inteligência ameaça.      |
| **Descrição de Ameaças**    |   A descrição da ameaça [maliciosa,](#MaliciousIP) de acordo com o feed da inteligência de ameaça.      |
| **AmeaçasEverity** | A severidade da ameaça para o [MaliciousIP](#MaliciousIP), de acordo com o feed de inteligência ameaça no momento da ingestão de registos. |
|     |         |

### <a name="additional-enrichment-fields"></a>Campos de enriquecimento adicionais

|Nome de campo CommonSecurityLog  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  Sempre vazio, apoiado para integração com a CiscoASA. <br>Para obter mais informações sobre os valores de gravidade do registo, consulte o campo [LogSeverity.](#logseverity)       |
|**RemoteIP**     |     O endereço IP remoto. <br>Este valor baseia-se no campo [CommunicationDirection,](#communicationdirection) se possível.     |
|**RemotePort**     |   A porta remota. <br>Este valor baseia-se no campo [CommunicationDirection,](#communicationdirection) se possível.      |
|**Simplificação DeviceAction**     |   Simplifica o valor [de Aacção do Dispositivo](#deviceaction) a um conjunto estático de valores, mantendo o valor original no campo [DeviceAction.](#deviceaction) <br>Por exemplo: `Denied`  >  `Deny` .      |
|**SourceSystem**     | Sempre definido como **OpsManager.**        |
|     |         |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [Conecte a sua solução externa utilizando o Formato Comum de Eventos.](connect-common-event-format.md)
