---
title: Registos de auditoria para Azure Data Box, Azure Data Box Eventos pesados Microsoft Docs
description: Descreve os registos completos de auditoria para a Data Box que são recolhidos nas várias fases da sua caixa de dados Azure e da encomenda pesada da Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209976"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Registos de auditoria para a sua Caixa de Dados Azure e Caixa de Dados Azure Pesada

Os registos são imutáveis, registos com carimpados de eventos discretos que aconteceram ao longo do tempo. Os registos contêm informações de diagnóstico, auditoria e segurança do seu dispositivo.  

Uma Caixa de Dados ou Caixa de Dados A encomenda pesada passa pelos seguintes passos durante o seu funcionamento: encomenda, configuração, cópia de dados, retorno, upload para Azure e verificar, e apagamento de dados. Para cada um destes passos, todos os eventos são auditados e registados.

Este artigo contém informações sobre os registos de auditoria da Caixa de Dados, incluindo os tipos de registos e as informações recolhidas, bem como a localização dos registos. 

A informação neste artigo aplica-se a ambos, Data Box e Data Box Heavy. Nas secções seguintes, quaisquer referências à Caixa de Dados também se aplicam à Data Box Heavy. Os registos recolhidos do serviço Data Box em execução em Azure não estão abrangidos por este artigo. 


## <a name="about-audit-logs"></a>Sobre registos de auditoria 

Na sua Caixa de Dados, são recolhidos os seguintes registos:

- **Registos do sistema** - A Data Box é um dispositivo baseado no Windows, todos os eventos de hardware, software e sistema estão registados. Um conjunto destes eventos é recolhido e reportado nos registos de auditoria do sistema. 

- **Segurança** - Caixa de Dados sendo um dispositivo baseado no Windows, todos os eventos de segurança são registados. Um conjunto destes eventos é recolhido e reportado nos registos de auditoria de segurança. 

- **Aplicação** - Estes registos são específicos apenas da Caixa de Dados. Estes registos contêm todos os eventos gerados no dispositivo em resposta aos serviços da Caixa de Dados que estão a ser em execução.

Cada um destes registos é discutido na secção seguinte.

### <a name="system-logs"></a>Registos do sistema

Os seguintes IDs de evento de registo do sistema são recolhidos como registos de auditoria do sistema na sua Caixa de Dados:

|Nome do fornecedor de eventos     |ID do evento recolhido   |Descrição do evento   |
|-------------------|----------|----------------|
|Microsoft-Windows-Kernel-General|12  |Hora utc quando o SO foi reiniciado.   |
|                                |13  |Hora utc quando o SO foi desligado. |
|    |                              |
|Microsoft-Windows-Kernel-Power  |41  |Sistema reiniciado sem uma paragem limpa.| 
|    |                              |
|Microsoft-Windows-BitLocker-Driver|Todos|    |

### <a name="security-logs"></a>Registos de segurança

Os seguintes IDs de evento de registo de segurança são recolhidos como registos de auditoria de segurança na sua Caixa de Dados:

|Nome do fornecedor de eventos                   |ID do evento recolhido    |Descrição do evento       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-Auditing   |4624        |Início de são bem sucedido. |
|                                      |4625        |Uma logon de conta falhou. Nome de utilizador desconhecido ou má senha. |
|                                     

### <a name="application-logs"></a>Registos de aplicações

Os iDs de registo de aplicações seguintes são recolhidos como parte dos registos de auditoria de pacotes na sua Caixa de Dados.     

- **Microsoft-Azure-DataBox-OOBE-Auditing** - contém os eventos que ocorrem na UI local. 
- **Microsoft-Azure-DataBox-Reprovision-Audit** - contém eventos relacionados com a reprovisionamento do dispositivo Data Box. A reprovisionamento da Caixa de Dados ocorre quando o dispositivo é reiniciado através da UI local. Escolha esta opção quando pretende apagar os dados que copiou removendo as ações existentes e recriando as ações como parte da reprovisionamento ou do reset do dispositivo.
- **Microsoft-Azure-DataBox-HcsMgmt-Audit** - contém eventos relacionados apenas com o passo **de Preparação para** o navio antes de o dispositivo ser enviado de volta para o centro de dados Azure. 
- **Microsoft-Azure-DataBox-IfxAudit** - contém as mensagens registadas por diferentes entidades do produto sobre os trabalhos, registos que indicam mais informações sobre o que está a acontecer em alguns dos fluxos.

Aqui está uma tabela que resume os vários fornecedores de eventos e os correspondentes IDs de evento que são recolhidos em cada caso.

|Nome do fornecedor de eventos    |ID do Evento    | Notas |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-Auditing |4624        |Início de são bem sucedido.|
|                                      |4625        |Uma logon de conta falhou. Nome de utilizador desconhecido ou má senha.|
|                                     |4634        |Iniciar o evento.|
|                                   |  | |
|Microsoft-Azure-DataBox-Reprovision-Audit    |65001       |Evento de reprovisionamento bem sucedido.|
|                                                  |65002       |Falhou na reprovisionação do evento.|
|                                                  |                 |         |
|Microsoft-Azure-DataBox-HcsMgmt-Audit        |65003       |Prepare-se para o evento estatal notStarted, InProgress, Failed, Canceled, Succeeded, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |Todos |Todos os eventos são registados com registo de auditoria API em código |

Aqui está um exemplo do registo de auditoria do Quadro de Instrumentação (IFX):

|     Tarefa/Trabalho/API                              |     Eventos registados                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     Limpeza                                   |     Os eventos relacionados com o início, conclusão ou falha de um trabalho de limpeza são registados. |                                              
|     Preparar dispositivo para envio de clientes    |     Os eventos relacionados com o início, conclusão ou falha de trabalho para preparar o dispositivo para envio são registados. |
|     Aprovisionar                                 |     Os eventos relacionados com o início, conclusão ou falha de um trabalho de fornecimento de dispositivos são registados.|
|     Trabalho de pacote de auditoria                       |     Os eventos relacionados com o início, conclusão ou falha de um trabalho de pacote de auditoria que cria registos de cadeia de custódia são registados.|
|     Sobrepor-se ao disco                          |     A não substituição do disco está registada.|
|     Ativar ou desativar o PowerShell remoto     |     Os eventos relacionados com a ativação ou desativação do PowerShell remoto no dispositivo estão registados. |
|     Obtenha detalhes da fase de instalação               |     Os eventos relacionados com a instalação de software no dispositivo por fases são registados no datacenter Azure.|
|     Desbloqueie ou bloqueie o volume BitLocker           |     Os eventos são registados para indicar o estado bitLocker do volume *de basevolume* e *hcsdata.*|
|     Disquete de sanitize                              |     Os eventos relacionados com a falha de discos físicos que não puderam ser apagados, e os eventos quando todos os discos físicos do dispositivo são apagados com sucesso, são registados. |
|     Ativar ou desativar o utilizador local               |     Os eventos relacionados com a ativação ou desativação de contas de utilizadores locais para StorSimpleAdmin e PodSupportAdminUser estão registados.| 
|     Reset da palavra-passe                          |     Os eventos relacionados com o reset de palavra-passe bem sucedido ou falhado para o utilizador local StorSimpleAdmin estão registados. |


Além dos registos de auditoria ifx, os registos de auditoria da cadeia de custódia também são recolhidos para a Data Box. Estes registos não podem ser vistos em tempo real, mas apenas após a conclusão do trabalho e os dados são apagados dos discos da Caixa de Dados. Estes registos contêm um subconjunto das informações contidas nos registos de auditoria ifx.

Para obter mais informações sobre a cadeia de registos de auditoria da custódia, consulte [Obter registos de cadeia de custódia após o apagamento dos dados](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure).

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>Aceder aos registos de auditoria

Estes registos são armazenados em Azure e não podem ser acedidos diretamente. Se precisar de aceder a estes registos, preencha um bilhete de apoio. Para obter mais informações, consulte [contacte o Microsoft Support](data-box-disk-contact-microsoft-support.md). 

Assim que o bilhete de suporte for arquivado, a Microsoft irá descarregar e fornecer-lhe acesso a estes registos.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [resolver problemas na sua Caixa de Dados e Caixa de Dados Pesada](data-box-troubleshoot.md).
