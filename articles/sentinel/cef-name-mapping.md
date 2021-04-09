---
title: Chave de formato de evento comum (CEF) e mapeamento de campo CommonSecurityLog
description: Este artigo mapeia as chaves CEF dos nomes de campo correspondentes no CommonSecurityLog em Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 6c23fe86af030d371e12914062bb9558e8db3484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104776301"
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
|DeviceEventClassID     |   DeviceEventClassID     |   String ou inteiro que serve como um identificador único por tipo de evento.      |
| destinoDnsDomain    | DestinationDnsDomain        |   A parte DNS do nome de domínio totalmente qualificado (FQDN).      |
| nome de serviço de destino | Nome do DestinationService | O serviço que é alvo do evento. Por exemplo, `sshd`.|
| destinoTranslatedAddress | DestinoTranslatadoAddress | Identifica o destino traduzido referido pelo evento numa rede IP, como um endereço IP IPv4. |
| destinoTranslatedPort | DestinationTranslatedPort | Porto, após tradução, como uma firewall. <br>Números de porta válidos: `0` - `65535` |
| direção de dispositivos | <a name="communicationdirection"></a> Direção de Comunicação | Qualquer informação sobre o rumo que a comunicação observada tomou. Valores válidos: <br>- `0` = Entrada <br>- `1` = Saída |
| deviceDnsDomain | DeviceDnsDomain | A parte do domínio DNS do nome de domínio completo qualificado (FQDN) |
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
|Gravidade     |  <a name="logseverity"></a> LogSeverity       |  Uma corda ou inteiro que descreve a importância do evento.<br><br> Valores de cordas válidos: `Unknown` `Low` , , `Medium` , `High``Very-High` <br><br>Valores inteiros válidos são: `0` - `3` = Baixo, `4` - `6` = Médio, `7` - `8` = Alto, `9` - `10` = Very-High |
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
| tipo | EventType | Tipo de evento. Os valores de valor incluem: <br>- `0`: evento base <br>- `1`: agregado <br>- `2`: evento de correlação <br>- `3`: evento de ação <br><br>**Nota:** Este evento pode ser omitido para eventos de base. |
| | | |

## <a name="unmapped-fields"></a>Campos não mapeados

Os seguintes nomes de campo **CommonSecurityLog** não têm mapeamentos nas teclas CEF:


|Nome de campo CommonSecurityLog  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  Sempre vazio, apoiado para integração com a CiscoASA. <br>Para obter mais informações sobre os valores de gravidade do registo, consulte o campo [LogSeverity.](#logseverity)       |
|**RemoteIP**     |     O endereço IP remoto. <br>Este valor baseia-se no campo [CommunicationDirection,](#communicationdirection) se possível.     |
|**RemotePort**     |   A porta remota. <br>Este valor baseia-se no campo [CommunicationDirection,](#communicationdirection) se possível.      |
|**Simplificação DeviceAction**     |   Simplifica o valor [de Aacção do Dispositivo](#deviceaction) a um conjunto estático de valores, mantendo o valor original no campo [DeviceAction.](#deviceaction) <br>Por exemplo: `Denied`  >  `Deny` .      |
|     |         |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [Conecte a sua solução externa utilizando o Formato Comum de Eventos.](connect-common-event-format.md)
