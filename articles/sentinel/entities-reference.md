---
title: Entidade Azure Sentinel tipo referência | Microsoft Docs
description: Este artigo apresenta os tipos de entidades Azure Sentinel e os seus identificadores necessários.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456308"
---
# <a name="azure-sentinel-entity-types-reference"></a>Referência de tipos de entidade Azure Sentinel

## <a name="entity-types-and-identifiers"></a>Tipos e identificadores de entidades

A tabela a seguir mostra os tipos de **entidades** atualmente disponíveis para mapeamento no Azure Sentinel, e os **atributos** disponíveis como **identificadores** para cada tipo de entidade - que aparecem na lista de desacompossos **dos Identifiers** na secção de mapeamento de [entidades](map-data-fields-to-entities.md) do assistente de [regras de análise](tutorial-detect-threats-custom.md).

Cada um dos identificadores na coluna de **identificadores necessários** é minimamente necessário para identificar a sua entidade. No entanto, um identificador necessário não pode, por si só, ser suficiente para fornecer uma identificação *única.* Quanto mais identificadores usados, maior a probabilidade de identificação única. Pode utilizar até três identificadores para um único mapeamento de entidade.

Para obter melhores resultados - para uma identificação única garantida - deve utilizar identificadores da coluna **de identificadores mais forte** sempre que possível. A utilização de múltiplos identificadores fortes permite a correlação entre identificadores fortes de fontes de dados variadas e esquemas. Isto, por sua vez, permite que a Azure Sentinel forneça informações mais abrangentes para uma determinada entidade.

| Tipo de entidade | Identificadores | Identificadores necessários | Identificadores mais fortes |
| - | - | - | - |
| [**Conta de utilizador**](#user-account)<br>*(Conta)* | Name<br>FullName<br>NTDomain<br>DnsDomain<br>UPNS sufixo<br>Rio Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjetoGuid | FullName<br>Rio Sid<br>Name<br>AadUserId<br>PUID<br>ObjetoGuid | Nome + NTDomain<br>Nome + UPNS sulés<br>AADUserId<br>Rio Sid |
| [**Anfitrião**](#host) | DnsDomain<br>NTDomain<br>Nome do Anfitrião<br>FullName<br>NomeNetBios<br>AzureID<br>OMSAGENTID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>Nome do Anfitrião<br>NomeNetBios<br>AzureID<br>OMSAGENTID | Nome anfitrião + NTDomain<br>Nome anfitrião + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>OMSAGENTID |
| [**Endereço IP**](#ip-address)<br>*(IP)* | Endereço | Endereço | |
| [**Malware**](#malware) | Name<br>Categoria | Name | |
| [**Ficheiro**](#file) | Diretório<br>Name | Name | |
| [**Processo**](#process) | ProcessId<br>Linha de Comando<br>ElevaçãoToken<br>CriaçãoTimeUtc | Linha de Comando<br>ProcessId | |
| [**Aplicação em nuvem**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Name<br>InstanceName | AppId<br>Name | |
| [**Nome de domínio**](#domain-name)<br>*(DNS)* | DomainName | DomainName | |
| [**Recurso do Azure**](#azure-resource) | ResourceId | ResourceId | |
| [**Hash de ficheiro**](#file-hash)<br>*(FileHash)* | Algoritmo<br>Valor | Algoritmo + Valor | |
| [**Chave de registo**](#registry-key) | Hive<br>Chave | Hive<br>Chave | Colmeia + Chave |
| [**Valor do registo**](#registry-value) | Name<br>Valor<br>ValueType | Name | |
| [**Grupo de segurança**](#security-group) | Nome distinto<br>SID<br>ObjetoGuid | Nome distinto<br>SID<br>ObjetoGuid | |
| [**URL**](#url) | Url | Url | |
| [**Dispositivo IoT**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Origem<br>FonteRef<br>Fabricante<br>Modelação<br>OperatingSystem<br>IpAddress<br>MacAddress<br>Protocolos<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**Caixa de correio**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Upn<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**Aglomerado de correio**](#mail-cluster) | NetworkMessageIds<br>CondeByDeliveryStatus<br>CondeByThreatType<br>CondeByProtectionStatus<br>Ameaças<br>Consulta<br>Horário de consulta<br>MailCount<br>IsVolumeAnomaly<br>Origem<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Consulta<br>Origem | Consulta + Fonte |
| [**Mensagem de correio**](#mail-message) | Destinatário<br>Urls<br>Ameaças<br>Remetente<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>RecebeuDate<br>NetworkMessageId<br>InternetMessageId<br>Assunto<br>BodyFingerPrintBin1<br>BodyFingerPrintBin2<br>BodyFingerPrintBin3<br>BodyFingerPrintBin4<br>BodyFingerPrintBin5<br>Anti-spamDirection<br>Reação de Entrega<br>EntregaLocação<br>Linguagem<br>AmeaçasDetectionMethods | NetworkMessageId<br>Destinatário | NetworkMessageId + Destinatário |
| [**Correio de submissão**](#submission-mail) | SubmissãoId<br>SubmissãoDa<br>Submetidar<br>NetworkMessageId<br>CarimboDeDataEHora<br>Destinatário<br>Remetente<br>SenderIp<br>Assunto<br>Logotipo | SubmissãoId<br>NetworkMessageId<br>Destinatário<br>Submetidar |  |
|

## <a name="entity-type-schemas"></a>Esquemas de tipo de entidade

Segue-se um olhar mais aprofundado sobre os esquemas completos de cada tipo de entidade. Irá notar que muitos destes esquemas incluem links para outros tipos de entidades - por exemplo, o esquema da conta do Utilizador inclui um link para o tipo de entidade anfitrião, uma vez que um atributo de uma conta de utilizador é o anfitrião em que está definido. Estas entidades ligadas externamente não podem ser usadas como identificadores para o mapeamento de entidades, mas são muito úteis para dar uma imagem completa de entidades nas páginas das entidades e no gráfico de investigação.

> [!NOTE]
> Um ponto de interrogação seguindo o valor na coluna **Tipo** indica que o campo é anulado.

## <a name="user-account"></a>Conta de utilizador

*Nome da entidade: Conta*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'conta' |
| Name | String | O nome da conta. Este campo deve conter apenas o nome sem qualquer domínio adicionado ao mesmo. |
| *FullName* | *N/A* | *Não faz parte do esquema, incluído para retrocompatibilidade com versão antiga do mapeamento de entidades.*
| NTDomain | String | O nome de domínio NETBIOS tal como aparece no formato de alerta – nome de utilizador domínio\. Exemplos: Finanças, NT AUTHORITY |
| DnsDomain | String | O nome DNS de domínio totalmente qualificado. Exemplos: finance.contoso.com |
| UPNS sufixo | String | O sufixo principal do utilizador para a conta. Em alguns casos, este é também o nome de domínio. Exemplos: contoso.com |
| Anfitrião | Entidade | O anfitrião que contém a conta, se for uma conta local. |
| Rio Sid | String | O identificador de segurança da conta, como o S-1-5-18. |
| AadTenantId | O Guid? | A identificação do inquilino da Azure, se conhecido. |
| AadUserId | O Guid? | A identificação do objeto de conta Azure, se conhecido. |
| PUID | O Guid? | O ID do utilizador do passaporte Azure, se conhecido. |
| IsDomainJoined | O Bool? | Determina se esta é uma conta de domínio. |
| DisplayName | String | O nome de exibição da conta. |
| ObjetoGuid | O Guid? | O atributo objectGUID é um atributo de valor único que é o identificador único para o objeto, atribuído pelo Ative Directory. |
|

Identificadores fortes de uma entidade de conta:

- Nome + UPNS sulés
- AadUserId
- Sid + Anfitrião (obrigatório para SIDs de contas incorporadas)
- Sid (com exceção dos SIDs de contas incorporadas)
- Nome + NTDomain (a menos que NTDomain seja um domínio construído, por exemplo "Grupo de Trabalho")
- Nome + Anfitrião (se NTDomain é um domínio construído, por exemplo "Grupo de Trabalho")
- Nome + DnsDomain
- PUID
- ObjetoGuid

Identificadores fracos de uma entidade de conta:

- Name

## <a name="host"></a>Anfitrião

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'Anfitrião' |
| DnsDomain | String | O domínio DNS a que este hospedeiro pertence. Deve conter o sufixo DNS completo para o domínio, se conhecido. |
| NTDomain | String | O domínio NT a que este hospedeiro pertence. |
| Nome do Anfitrião | String | O nome de anfitrião sem o sufixo de domínio. |
| *FullName* | *N/A* | *Não faz parte do esquema, incluído para retrocompatibilidade com versão antiga do mapeamento de entidades.*
| NomeNetBios | String | O nome de anfitrião (pré-Windows 2000). |
| IoTDevice | Entidade | A entidade dispositivo IoT (se este hospedeiro representar um Dispositivo IoT). |
| AzureID | String | O ID de recurso Azure do VM, se conhecido. |
| OMSAGENTID | String | O ID do agente OMS, se o anfitrião tiver o agente OMS instalado. |
| OSFamily | O Enum? | Um dos seguintes valores: <li>Linux<li>Windows<li>Android<li>iOS |
| OSVersion | String | Uma representação de texto livre do sistema operativo.<br>Este campo destina-se a manter versões específicas que são mais finas do que os OSFamily, ou valores futuros não suportados pela enumeração DAFamily. |
| IsDomainJoined | Booleano | Determina se este hospedeiro pertence a um domínio. |
|

Identificadores fortes de uma entidade hospedeira:
- Nome anfitrião + NTDomain
- Nome anfitrião + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- OMSAGENTID
- IoTDevice (não suportado para mapeamento de entidades)

Identificadores fracos de uma entidade hospedeira:
- Nome do Anfitrião
- NomeNetBios

## <a name="ip-address"></a>Endereço IP

*Nome da entidade: IP*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'ip' |
| Endereço | String | O endereço IP como string, por exemplo, 127.0.0.1 (quer no IPv4 quer no IPv6). |
| Localização | GeoLocalização | O contexto de geolocalização ligado à entidade IP. |
|

Identificadores fortes de uma entidade IP:
- Endereço

## <a name="malware"></a>Software maligno

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'malware' |
| Name | String | O nome do malware pelo fornecedor, tal como `Win32/Toga!rfn` . |
| Categoria | String | A categoria de malware pelo fornecedor, por exemplo, Trojan. |
| Ficheiros | Lista\<Entity> | Lista de entidades de ficheiros ligadas nas quais o malware foi encontrado. Pode conter as entidades de Arquivo em linha ou como referência.<br>Consulte a entidade de Arquivo para obter mais detalhes sobre a estrutura. |
| Processos | Lista\<Entity> | Lista de entidades de processos ligadas nas quais o malware foi encontrado. Isto seria frequentemente usado quando o alerta desencadeava atividade sem ficheiros.<br>Consulte a entidade [de processo](#process) para obter mais detalhes sobre a estrutura. |
|

Identificadores fortes de uma entidade de malware:

- Nome + Categoria

## <a name="file"></a>Ficheiro

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'arquivo' |
| Diretório | String | O caminho completo para o arquivo. |
| Name | String | O nome do ficheiro sem o caminho (alguns alertas podem não incluir o caminho). |
| Anfitrião | Entidade | O anfitrião no qual o ficheiro foi armazenado. |
| FileHashes | &lt;Entidade de Lista&gt; | O ficheiro temes associado a este ficheiro. |
|

Identificadores fortes de uma entidade de ficheiros:
- Nome + Diretório
- Nome + Filehash
- Nome + Diretório + FileHash

## <a name="process"></a>Processo

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'processo' |
| ProcessId | String | A identificação do processo. |
| Linha de Comando | String | A linha de comando usada para criar o processo. |
| ElevaçãoToken | O Enum? | O símbolo de elevação associado ao processo.<br>Valores possíveis:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CriaçãoTimeUtc | Hora do encontro? | O tempo em que o processo começou a decorrer. |
| Ficheiro de imagem | Entidade (Arquivo) | Pode conter a inline da entidade de Ficheiro ou como referência.<br>Consulte a entidade de Arquivo para obter mais detalhes sobre a estrutura. |
| Conta | Entidade | A conta que está a executar os processos.<br>Pode conter a entidade [de Conta](#user-account) em linha ou como referência.<br>Consulte a entidade [conta](#user-account) para obter mais detalhes sobre a estrutura. |
| Processo dos Pais | Entidade (Processo) | A entidade de processo dos pais. <br>Pode conter dados parciais, ou seja, apenas o PID. |
| Anfitrião | Entidade | O anfitrião em que o processo estava a decorrer. |
| LogonSsion | Entidade (HostLogonSession) | A sessão em que o processo estava a decorrer. |
|

Identificadores fortes de uma entidade de processo:

- Anfitrião + ProcessId + CriaçãoTimeutc
- Anfitrião + ParentProcessId + CreationTimeUtc + CommandLine
- Anfitrião + ProcessId + CriaçãoTimeUtc + ImageFile
- Anfitrião + ProcessId + CriaçãoTimeUtc + ImageFile.FileHash

Identificadores fracos de uma entidade de processo:

- ProcessId + CreationTimeUtc + CommandLine (e sem Anfitrião)
- ProcessId + CreationTimeUtc + ImageFile (e sem Anfitrião)

## <a name="cloud-application"></a>Aplicação em nuvem

*Nome da entidade: CloudApplication*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'aplicação em nuvem' |
| AppId | int | O identificador técnico da aplicação. Este deve ser um dos valores definidos na lista de identificadores de [aplicações](#cloud-application-identifiers)em nuvem . O valor para o campo AppId é opcional. |
| Name | String | O nome da aplicação de nuvem relacionada. O valor do nome da aplicação é opcional. |
| InstanceName | String | O nome de instância definido pelo utilizador da aplicação cloud. É frequentemente usado para distinguir entre várias aplicações do mesmo tipo que um cliente tem. |
|

Identificadores fortes de uma entidade de aplicação em nuvem:
 - AppId (sem nome de exemplo)
 - Nome (sem nome de exemplo)
 - AppId + InstanceName
 - Nome + Nome

## <a name="domain-name"></a>Nome de domínio

*Nome da entidade: DNS*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'dns' |
| DomainName | String | O nome do registo dns associado ao alerta. |
| IpAddress | Entidade de Lista &lt; (IP)&gt; | Entidades correspondentes aos endereços IP resolvidos. |
| DnsServerIp | Entidade (IP) | Uma entidade que representa o servidor DNS a resolver o pedido. |
| HostIpAddress | Entidade (IP) | Uma entidade que representa o cliente de pedido do DNS. |
|

Identificadores fortes de uma entidade DNS:
- Nome de domínio + DnsServerIp + HostIpAddress

Identificadores fracos de uma entidade DNS:
- Nome de domínio + HostIpAddress

## <a name="azure-resource"></a>Recurso do Azure

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'recurso azul' |
| ResourceId | String | O ID de recurso Azure do recurso. |
| SubscriptionId | String | A identificação de assinatura do recurso. |
| TryGetResourceGroup | Booleano | O valor do grupo de recursos se existir. |
| TryGetProvider | Booleano | O fornecedor valoriza se existir. |
| TryGetName | Booleano | O valor do nome se existir. |
|

Identificadores fortes de uma entidade de recursos Azure:
- ResourceId

## <a name="file-hash"></a>Hash de ficheiro

*Nome da entidade: FileHash*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'filehash' |
| Algoritmo | Enumeração | O tipo de algoritmo de haxixe. Valores possíveis:<li>Desconhecido<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Valor | String | O valor de haxixe. |
|

Identificadores fortes de uma entidade de haxixe de ficheiro:
- Algoritmo + Valor

## <a name="registry-key"></a>Chave do registo

*Nome da entidade: RegistryKey*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | «chave de registo» |
| Hive | O Enum? | Um dos seguintes valores:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Chave | String | O caminho chave do registo. |
|

Identificadores fortes de uma entidade-chave de registo:
- Colmeia + Chave

## <a name="registry-value"></a>Valor de registo

*Nome da entidade: RegistryValue*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | «valor do registo» |
| Chave | Entidade (RegistryKey) | A entidade chave do registo. |
| Name | String | O nome do valor do registo. |
| Valor | String | Representação formatada em cadeia dos dados de valor. |
| ValueType | O Enum? | Um dos seguintes valores:<li>String<li>Binário<li>DWord<li>Qword<li>MultiString<li>ExpandString<li>Nenhum<li>Desconhecido<br>Os valores devem estar em conformidade com a enumeração microsoft.Win32.RegistryValueKind. |
|

Identificadores fortes de uma entidade de valor registador:
- Chave + Nome

Identificadores fracos de uma entidade de valor registador:
- Nome (sem chave)

## <a name="security-group"></a>Grupo de segurança

*Nome da entidade: SecurityGroup*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | «grupo de segurança» |
| Nome distinto | String | O nome distinto do grupo. |
| SID | String | O atributo SID é um atributo de valor único que especifica o identificador de segurança (SID) do grupo. |
| ObjetoGuid | O Guid? | O atributo objectGUID é um atributo de valor único que é o identificador único para o objeto, atribuído pelo Ative Directory. |
|

Identificadores fortes de uma entidade do grupo de segurança:
 - Nome distinto
 - SID
 - ObjetoGuid

## <a name="url"></a>URL

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'url' |
| Url | Uri | Um URL completo que a entidade aponta. |
|

Identificadores fortes de uma entidade URL:
- Url (quando um URL absoluto)

Identificadores fracos de uma entidade URL:
- Url (quando um URL relativo)

## <a name="iot-device"></a>Dispositivo IoT

*Nome da entidade: IoTDevice*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'iotdevice' |
| IoTHub | Entidade (AzureResource) | A entidade AzureResource que representa o IoT Hub a que pertence o dispositivo pertence. |
| DeviceId | String | A identificação do dispositivo no contexto do IoT Hub. |
| DeviceName | String | O nome amigável do dispositivo. |
| IoTSecurityAgentId | O Guid? | A identificação do Defensor para o agente *IoT* a funcionar no dispositivo. |
| DeviceType | String | O tipo de dispositivo ('sensor de temperatura', 'freezer', 'turbina eólica', etc.). |
| Origem | String | A fonte (Microsoft/Fornecedor) da entidade do dispositivo. |
| FonteRef | Entidade (Url) | Uma referência URL ao item de origem onde o dispositivo é gerido. |
| Fabricante | String | O fabricante do dispositivo. |
| Modelação | String | O modelo do dispositivo. |
| OperatingSystem | String | O sistema operativo que o dispositivo está a funcionar. |
| IpAddress | Entidade (IP) | O endereço IP atual do dispositivo. |
| MacAddress | String | O endereço MAC do dispositivo. |
| Protocolos | Cadeia de &lt; listas&gt; | Uma lista de protocolos que o dispositivo suporta. |
| SerialNumber | String | O número de série do dispositivo. |
|

Identificadores fortes de uma entidade de dispositivo IoT:
- IoTHub + DeviceId

Identificadores fracos de uma entidade de dispositivo IoT:
- DeviceId (sem IoTHub)

## <a name="mailbox"></a>Mailbox

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'caixa de correio' |
| MailboxPrimaryAddress | String | O endereço principal da caixa de correio. |
| DisplayName | String | O nome de exibição da caixa de correio. |
| Upn | String | A caixa de correio é upn. |
| RiskLevel | O Enum? | O nível de risco desta caixa de correio. Valores possíveis:<li>Nenhum<li>Baixo<li>Médio<li>Alto |
| ExternalDirectoryObjectId | O Guid? | O identificador AzureAD da caixa de correio. Semelhante ao AadUserId na entidade Conta, mas esta propriedade é específica para objeto de caixa de correio no lado do Office. |
|

Identificadores fortes de uma entidade da caixa de correio:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>Aglomerado de correio

*Nome da entidade: MailCluster*

> [!NOTE]
> **MDO**  =  **Microsoft Defender para o Office 365**, anteriormente conhecido como Office 365 Advanced Threat Protection (O365 ATP).

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | «cluster de correio» |
| NetworkMessageIds | Cadeia IList &lt;&gt; | As identificações de mensagens de correio que fazem parte do cluster de correio. |
| CondeByDeliveryStatus | &lt;Cadeia Idtionária,Int&gt; | Contagem de mensagens de correio por representação de cadeia DeliveryStatus. |
| CondeByThreatType | &lt;Cadeia Idtionária,Int&gt; | Contagem de mensagens de correio por representação de cadeias ThreatType. |
| CondeByProtectionStatus | Corda Idictionária, &lt; longa&gt; | Contagem de mensagens de correio por estado de proteção de ameaças. |
| Ameaças | Cadeia IList &lt;&gt; | As ameaças de mensagens de correio que fazem parte do aglomerado de correio. |
| Consulta | String | A consulta que foi usada para identificar as mensagens do cluster de correio. |
| Horário de consulta | Hora do encontro? | A hora da consulta. |
| MailCount | Int? | O número de mensagens de correio que fazem parte do cluster de correio. |
| IsVolumeAnomaly | O Bool? | Determina se se trata de um aglomerado de correio de anomalia de volume. |
| Origem | String | A origem do cluster de correio (por defeito é 'O365 ATP'). |
| ClusterSourceIdentifier | String | A identificação da mensagem de rede do correio que é a fonte deste cluster de correio. |
| ClusterSourceType | String | O tipo de fonte do aglomerado de correio. Este mapa para a definição mailClusterSourceType a partir de MDO (ver nota acima). |
| ClusterQueryStartTime | Hora do encontro? | Hora de início do cluster - usada como hora de início para consulta de contagem de clusters. Normalmente data para o fim do tempo menos a definição DaysToLookBack do MDO (ver nota acima). |
| ClusterQueryEndTime | Hora do encontro? | Intervalo de cluster - usado como tempo final para consulta de contagem de clusters. Normalmente, os dados do correio são recebidos. |
| ClusterGroup | String | Corresponde à chave de consulta Kusto utilizada no MDO (ver nota acima). |
|

Identificadores fortes de uma entidade de cluster de correio:
- Consulta + Fonte

## <a name="mail-message"></a>Mensagem de correio

*Nome da entidade: MailMessage*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'Mensagem de correio' |
| Ficheiros | Ficheiro IList &lt;&gt; | As entidades de arquivo dos anexos desta mensagem de correio. |
| Destinatário | String | O destinatário desta mensagem de correio. No caso de vários destinatários, a mensagem de correio é copiada e cada cópia tem um destinatário. |
| Urls | Cadeia IList &lt;&gt; | Os URLs contidos nesta mensagem de correio. |
| Ameaças | Cadeia IList &lt;&gt; | As ameaças contidas nesta mensagem de correio. |
| Remetente | String | O endereço de e-mail do remetente. |
| P1Sender | String | ID de e-mail de utilizador (delegado) que enviou este e-mail "em nome do utilizador P2 (primário)". Se o e-mail não for enviado por delegado, este valor é igual ao P2Sender. |
| P1SenderDisplayName | String | Nome de exibição do utilizador (delegado) que enviou este e-mail "em nome do utilizador P2 (primário)". Representado no cabeçalho de e-mail pela propriedade "OnbehalfofSenderDisplayName". |
| P1SenderDomain | String | Domínio de e-mail do utilizador (delegado) que enviou este e-mail "em nome do utilizador P2 (primário)". Se o e-mail não for enviado por delegado, este valor é igual ao P2SenderDomain. |
| P2Sender | String | E-mail do utilizador (primário) em nome de quem este e-mail foi enviado. |
| P2SenderDisplayName | String | Mostrar o nome do utilizador (primário) em nome de quem este e-mail foi enviado. Se o e-mail não for enviado por delegado, este representa o nome de exibição do remetente. |
| P2SenderDomain | String | Domínio de e-mail do utilizador (primário) em nome de quem este e-mail foi enviado. Se o e-mail não for enviado por delegado, este representa o domínio do remetente. |
| SenderIP | String | O endereço IP do remetente. |
| RecebeuDate | DateTime | A data recebida desta mensagem. |
| NetworkMessageId | O Guid? | A identificação da mensagem de rede desta mensagem de correio. |
| InternetMessageId | String | A identificação da mensagem de internet desta mensagem de correio. |
| Assunto | String | O assunto desta mensagem de correio. |
| BodyFingerPrintBin1<br>BodyFingerPrintBin2<br>BodyFingerPrintBin3<br>BodyFingerPrintBin4<br>BodyFingerPrintBin5 | O UInt? | Utilizado pelo Microsoft Defender para o Office 365 para encontrar mensagens de correio correspondentes ou similares. |
| Anti-spamDirection | O Enum? | A direccionalidade desta mensagem de correio. Valores possíveis:<li>Desconhecido<li>Entrada<li>Saída<li>Intraorg (interno) |
| Reação de Entrega | O Enum? | A ação de entrega desta mensagem de correio. Valores possíveis:<li>Desconhecido<li>EntregueAsSpam<li>Entregue<li>Bloqueado<li>Substituído |
| EntregaLocação | O Enum? | O local de entrega desta mensagem de correio. Valores possíveis:<li>Desconhecido<li>A Receber<li>JunkFolder<li>DeletedFolder<li>Quarentena<li>Externo<li>Com falhas<li>Caiu<li>Reencaminhado |
| Linguagem | String | A idioma em que o conteúdo do correio é escrito. |
| AmeaçasDetectionMethods | Cadeia IList &lt;&gt; | A lista de métodos de deteção de ameaças aplicada neste correio. |
|

Identificadores fortes de uma entidade de mensagens de correio:
- NetworkMessageId + Destinatário

## <a name="submission-mail"></a>Correio de submissão

*Nome da entidade: SubmissãoMail*

| Campo | Tipo | Descrição |
| ----- | ---- | ----------- |
| Tipo | String | 'SubmissãoMail' |
| SubmissãoId | O Guid? | A identificação de submissão. |
| SubmissãoDa | Hora do encontro? | Data reportada para esta submissão. |
| Submetidar | String | O endereço de e-mail do enviesador. |
| NetworkMessageId | O Guid? | O ID de mensagem de rede de e-mail a que pertence a submissão. |
| CarimboDeDataEHora | Hora do encontro? | O carimbo da hora quando a mensagem é recebida (Correio). |
| Destinatário | String | O destinatário do correio. |
| Remetente | String | O remetente do correio. |
| SenderIp | String | O IP do remetente. |
| Assunto | String | O assunto do correio de submissão. |
| Logotipo | String | O tipo de submissão para o caso dado. Este mapa para Junk, Phish, Malware ou NotJunk. |
|

Identificadores fortes de uma entidade submissãoMail:
- SubmissãoId, Submitter, NetworkMessageId, Destinatário

## <a name="cloud-application-identifiers"></a>Identificadores de aplicações em nuvem

A lista que se segue define os identificadores para aplicações em nuvem conhecidas. O valor de ID da aplicação é usado como identificador de [entidade de aplicação em nuvem.](#cloud-application)

|ID da Aplicação|Name|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive Software|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote Online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive para Empresas|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Plataforma Google Cloud|
|22930|Gmail|
|23004|Ciclo de vida de fusão automática|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|Microsoft Skype para Empresas|
|25988|Google Docs|
|26055|Microsoft Office 365 centro de administração|
|26060|Engrenagens OPSWAT|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Anúncio microsoft Azure|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|Emulador cas Proxy|
|28375|Microsoft Stream|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Talent|
|

## <a name="next-steps"></a>Passos seguintes

Neste documento aprendeu sobre a estrutura da entidade, identificadores e esquema em Azure Sentinel.

Saiba mais sobre [entidades](entities-in-azure-sentinel.md) e [mapeamento de entidades.](map-data-fields-to-entities.md) 
