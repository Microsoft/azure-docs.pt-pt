---
title: Tipos e descrições de alerta
description: Reveja as descrições do Defender para ioT Alert.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 4/8/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9ef7aa388d0f25adcafec1cb4a5b38dcfb8597a1
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210470"
---
# <a name="alert-types-and-descriptions"></a>Tipos e descrições de alerta

Este artigo descreve todos os tipos de alerta, que podem ser gerados a partir do Defender para motores IoT. Os alertas aparecem na janela Alertas, o que lhe permite gerir o evento de alerta. 

## <a name="policy-engine-alerts"></a>Alertas de motores de política

Os alertas do motor de política descrevem desvios do comportamento aprendido da rede de base.

| Título  | Descrição | Gravidade |
|--|--|--|
| Utilização anormal de endereços MAC | Um novo dispositivo de origem foi detetado na rede, mas não foi autorizado. | Menor |
| Beckhoff Software Alterado | Firmware foi atualizado num dispositivo de origem. Esta pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planeado. | Maior |
| Início de dados falhado | Uma tentativa falhada de login foi detetada de um dispositivo de origem para um servidor de destino. Isto pode ser o resultado de um erro humano, mas também pode indicar uma tentativa maliciosa de comprometer o servidor ou dados sobre o mesmo. | Maior |
| Versão de firmware emerson ROC alterada | Firmware foi atualizado num dispositivo de origem. Esta pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planeado. | Maior |
| Endereço externo dentro da rede comunicada com a Internet | Um dispositivo de origem definido como parte da sua rede está a comunicar com endereços de Internet. A fonte não está autorizada a comunicar com endereços de Internet. | Crítico |
| Dispositivo de campo descoberto inesperadamente | Um novo dispositivo de origem foi detetado na rede, mas não foi autorizado. | Maior |
| Alteração de firmware detetada | Firmware foi atualizado num dispositivo de origem. Esta pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planeado. | Maior |
| Versão do firmware alterada | Firmware foi atualizado num dispositivo de origem. Esta pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planeado. | Maior |
| Foxboro I/A Operação Não Autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Início de Sessão FTP falhou | Uma tentativa falhada de login foi detetada de um dispositivo de origem para um servidor de destino. Isto pode ser o resultado de um erro humano, mas também pode indicar uma tentativa maliciosa de comprometer o servidor ou dados sobre o mesmo. | Maior |
| Código de função levantado exceção não autorizada | Um dispositivo de origem (escravo) devolveu uma exceção a um dispositivo de destino (mestre). | Maior |
| Definições do tipo de mensagem GOOSE | As definições de mensagem (identificadas pelo ID do protocolo) foram alteradas num dispositivo de origem. | Aviso |
| Versão de firmware Honeywell alterada | Firmware foi atualizado num dispositivo de origem. Esta pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planeado. | Maior |
| Comunicação HTTP Ilegal | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Acesso à Internet Detetado | Um dispositivo de origem definido como parte da sua rede está a comunicar com endereços de Internet. A fonte não está autorizada a comunicar com endereços de Internet. | Maior |
| Versão do Firmware Mitsubishi alterada | Firmware foi atualizado num dispositivo de origem. Esta pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planeado. | Maior |
| Violação da faixa de endereços Modbus | Um dispositivo principal pediu acesso a um novo endereço de memória de escravos. | Maior |
| Versão de Firmware Modbus alterada | Firmware foi atualizado num dispositivo de origem. Esta pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planeado. | Maior |
| Nova Atividade Detetada - Classe CIP | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova Atividade Detetada - Serviço de Classe CIP | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova Atividade Detetada - Comando CIP PCCC | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Símbolo CIP | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Ligação EtherNet/IP I/O | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Comando do Protocolo EtherNet/IP | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Código de Mensagem GSM | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Códigos de Comando LonTalk | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova Descoberta do Porto | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Aviso |
| Nova atividade detetada - Variável da rede LonTalk | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova Atividade Detetada - Pedido de Dados de Ovação | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Comando de Leitura/Escrita (Grupo índice AMS) | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Comando de Leitura/Escrita (Offset do Índice AMS) | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Tipo de mensagem DeltaV não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Operação ROC DeltaV não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - Tipo de mensagem RPC não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova Atividade Detetada - Invocação não autorizada do Procedimento RPC | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - usando o comando do protocolo AMS | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - usando o comando Siemens SICAM | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - usando o comando do Protocolo Suitelink | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - usando sessões de protocolo suitelink | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Nova atividade detetada - usando o comando Yokogawa VNetIP | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Novo ativo detetado | Um novo dispositivo de origem foi detetado na rede, mas não foi autorizado. | Maior |
| Nova configuração do dispositivo LLDP | Um novo dispositivo de origem foi detetado na rede, mas não foi autorizado. | Maior |
| Nova Descoberta do Porto | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Aviso |
| Comando Não Autorizado Omron FINS | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Firmware S7 Plus PLC Alterado | Firmware foi atualizado num dispositivo de origem. Esta pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planeado. | Maior |
| Definições do tipo de mensagem de valores amostrados | As definições de mensagem (identificadas pelo ID do protocolo) foram alteradas num dispositivo de origem. | Aviso |
| Suspeita de integridade ilegal | Foi detetada uma varredura num dispositivo de origem DNP3 (outstation). Esta varredura não foi autorizada como o tráfego aprendido na sua rede. | Maior |
| Comando não autorizado da Ligação de Computador Toshiba | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Menor |
| Operação de ficheiro total de fluxo de fluxo não autorizado ABB | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Operação não autorizada de registo de fluxos totais ABB | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Acesso não autorizado ao Bloco de Dados Siemens S7 | Um dispositivo de origem tentou aceder a um recurso noutro dispositivo. Uma tentativa de acesso a este recurso entre estes dois dispositivos não foi autorizada como o tráfego aprendido na sua rede. | Aviso |
| Acesso não autorizado ao Objeto Siemens S7 Plus | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Acesso não autorizado à tag Wonderware | Um dispositivo de origem tentou aceder a um recurso noutro dispositivo. Uma tentativa de acesso a este recurso entre estes dois dispositivos não foi autorizada como o tráfego aprendido na sua rede. | Maior |
| Acesso não autorizado a objetos BACNet | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Rota BACNet não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Início de dados não autorizado | Foi detetada uma tentativa de login entre um cliente de origem e um servidor de destino. A comunicação entre estes dispositivos não foi autorizada como o tráfego aprendido na sua rede. | Maior |
| Operação de base de dados não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Operação Emerson ROC não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Acesso não autorizado a ficheiros GE SRTP | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Comando do Protocolo GE SRTP não autorizado | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Operação de memória do sistema GE SRTP não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Atividade HTTP não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Servidor HTTP não autorizado | Uma aplicação não autorizada foi detetada num dispositivo de origem. O pedido não foi autorizado como um pedido aprendido na sua rede. | Maior |
| Ação não autorizada http SOAP | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Agente de utilizador HTTP não autorizado | Uma aplicação não autorizada foi detetada num dispositivo de origem. O pedido não foi autorizado como um pedido aprendido na sua rede. | Maior |
| Conectividade não autorizada na Internet Detetada | Um dispositivo de origem definido como parte da sua rede está a comunicar com endereços de Internet. A fonte não está autorizada a comunicar com endereços de Internet. | Crítico |
| Comando Mitsubishi MELSEC não autorizado | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Acesso não autorizado do programa MMS | Um dispositivo de origem tentou aceder a um recurso noutro dispositivo. Uma tentativa de acesso a este recurso entre estes dois dispositivos não foi autorizada como o tráfego aprendido na sua rede. | Maior |
| Serviço MMS não autorizado | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Conexão multicast/difusão não autorizada | Foi detetada uma ligação Multicast/Broadcast entre um dispositivo de origem e outros dispositivos. A comunicação multicast/difusão não é autorizada. | Crítico |
| Consulta de nome não autorizado | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Atividade não autorizada da UA da OPC | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Pedido/Resposta não autorizado da UA UA | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| A operação não autorizada foi detetada por uma Regra Definida pelo Utilizador | O trânsito foi detetado entre dois dispositivos. Esta atividade não é autorizada com base numa Regra de Alerta Personalizado definida por um utilizador. | Maior |
| Leitura de configuração não autorizada de PLC | O dispositivo de origem não é definido como um dispositivo de programação, mas realizou uma operação de leitura/escrita num controlador de destino. As alterações de programação só devem ser efetuadas por dispositivos de programação. Pode ter sido instalada uma aplicação de programação neste dispositivo. | Aviso |
| Escrita de configuração não autorizada de PLC | O dispositivo de origem enviou um comando para ler/escrever o programa de um controlador de destino. Esta atividade não foi previamente vista. | Maior |
| Upload não autorizado do programa PLC | O dispositivo de origem enviou um comando para ler/escrever o programa de um controlador de destino. Esta atividade não foi previamente vista. | Maior |
| Programação não autorizada de PLC | O dispositivo de origem não é definido como um dispositivo de programação, mas realizou uma operação de leitura/escrita num controlador de destino. As alterações de programação só devem ser efetuadas por dispositivos de programação. Pode ter sido instalada uma aplicação de programação neste dispositivo. | Crítico |
| Tipo de moldura profinet não autorizado | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Comando Não Autorizado SAIA S-Bus | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Siemens S7 Execução não autorizada da Função de Controlo | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Siemens S7 execução não autorizada de função definida pelo utilizador | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Acesso não autorizado do Siemens S7 Plus ao bloco | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Operação Siemens S7 Plus não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Login SMB não autorizado | Foi detetada uma tentativa de login entre um cliente de origem e um servidor de destino. A comunicação entre estes dispositivos não foi autorizada como o tráfego aprendido na sua rede. | Maior |
| Operação SNMP não autorizada | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Acesso SSH não autorizado | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Processo do Windows não autorizado | Uma aplicação não autorizada foi detetada num dispositivo de origem. O pedido não foi autorizado como um pedido aprendido na sua rede. | Maior |
| Serviço de Windows não autorizado | Uma aplicação não autorizada foi detetada num dispositivo de origem. O pedido não foi autorizado como um pedido aprendido na sua rede. | Maior |
| A operação não autorizada foi detetada por uma Regra Definida pelo Utilizador | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros viola uma regra definida pelo utilizador | Maior |
| Extensão elétrica não autorizada de Modbus Schneider | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Utilização não autorizada de tipos asdu | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Utilização não autorizada do Código de Função DNP3 | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |
| Utilização não autorizada de indicação interna (IIN) | Um dispositivo de origem DNP3 (outstation) relatou uma indicação interna (IIN) que não autorizou como o tráfego aprendido na sua rede. | Maior |
| Utilização não autorizada do Código de Função Modbus | Foram detetados novos parâmetros de trânsito. Esta combinação de parâmetros não foi autorizada como o tráfego aprendido na sua rede. A seguinte combinação não é autorizada. | Maior |

## <a name="anomaly-engine-alerts"></a>Alertas de motor de anomalia

| Título | Descrição | Gravidade |
|--|--|--|
| Padrão de exceção anormal no escravo | Um número excessivo de erros foi detetado num dispositivo de origem. Isto pode ser o resultado de um problema operacional. | Menor |
| Comprimento anormal do cabeçalho HTTP | O dispositivo de origem enviou uma mensagem anormal. Isto pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Número anormal de parâmetros no cabeçalho HTTP | O dispositivo de origem enviou uma mensagem anormal. Isto pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Comportamento periódico anormal no canal de comunicação | Foi detetada uma alteração na frequência de comunicação entre os dispositivos de origem e de destino. | Menor |
| Cessação anormal das aplicações | Um número excessivo de comandos de paragem foi detetado num dispositivo de origem. Isto pode ser o resultado de um problema operacional ou de uma tentativa de manipular o dispositivo. | Maior |
| Largura de banda de tráfego anormal | A largura de banda anormal foi detetada num canal. A largura de banda parece ser significativamente mais baixa/superior do que anteriormente detetada. Para mais detalhes, trabalhe com o widget Total Bandwidth. | Aviso |
| Largura de banda de tráfego anormal entre dispositivos | A largura de banda anormal foi detetada num canal. A largura de banda parece ser significativamente mais baixa/superior do que anteriormente detetada. Para mais detalhes, trabalhe com o widget Total Bandwidth. | Aviso |
| Verificação de endereços detetada | Foi detetado um dispositivo de origem a digitalizar dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de digitalização de rede. | Crítico |
| Digitalização do endereço ARP Detetada | Foi detetado um dispositivo de origem a digitalizar dispositivos de rede utilizando o Protocolo de Resolução de Endereços (ARP). Este endereço do dispositivo não foi autorizado como endereço de digitalização ARP válido. | Crítico |
| Digitalização do endereço ARP Detetada | Foi detetado um dispositivo de origem a digitalizar dispositivos de rede utilizando o Protocolo de Resolução de Endereços (ARP). Este endereço do dispositivo não foi autorizado como endereço de digitalização ARP válido. | Crítico |
| Falsificação de ARP | Uma quantidade anormal de pacotes foi detetada na rede. Isto pode indicar um ataque, por exemplo, um ataque de falsificação de ARP ou inundação do ICMP. | Aviso |
| Tentativas de login excessivas | Um dispositivo de origem foi visto a realizar tentativas de login excessivas para um servidor de destino. Isto pode ser um ataque de força bruta. O servidor pode ser comprometido por um ator malicioso. | Crítico |
| Número excessivo de sessões | Um dispositivo de origem foi visto a realizar tentativas de login excessivas para um servidor de destino. Isto pode ser um ataque de força bruta. O servidor pode ser comprometido por um ator malicioso. | Crítico |
| Taxa de reinício excessivo de uma estação de serviço | Um número excessivo de comandos de reinício foi detetado num dispositivo de origem. Isto pode ser o resultado de um problema operacional ou de uma tentativa de manipular o dispositivo. | Maior |
| Tentativas de login excessivas de SMB | Um dispositivo de origem foi visto a realizar tentativas de login excessivas para um servidor de destino. Isto pode ser um ataque de força bruta. O servidor pode ser comprometido por um ator malicioso. | Crítico |
| Inundações do ICMP | Uma quantidade anormal de pacotes foi detetada na rede. Isto pode indicar um ataque, por exemplo, um ataque de falsificação de ARP ou inundação do ICMP. | Aviso |
| Conteúdo ilegal de cabeçalho HTTP | O dispositivo de origem iniciou um pedido inválido. | Crítico |
| Canal de Comunicação Inativo | Um canal de comunicação entre dois dispositivos estava inativo durante um período em que a atividade é geralmente vista. Isto pode indicar que o programa que gera este tráfego foi alterado, ou o programa pode estar indisponível. Recomenda-se rever a configuração do programa instalado e verificar se está configurado corretamente. | Aviso |
| Digitalização do endereço de longa duração detetada | Foi detetado um dispositivo de origem a digitalizar dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de digitalização de rede. | Crítico |
| Tentativa de adivinhação de senha detetada | Um dispositivo de origem foi visto a realizar tentativas de login excessivas para um servidor de destino. Isto pode ser um ataque de força bruta. O servidor pode ser comprometido por um ator malicioso. | Crítico |
| Varredura PLC Detetada | Foi detetado um dispositivo de origem a digitalizar dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de digitalização de rede. | Crítico |
| Varredura da porta detetada | Foi detetado um dispositivo de origem a digitalizar dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de digitalização de rede. | Crítico |
| Comprimento inesperado da mensagem | O dispositivo de origem enviou uma mensagem anormal. Isto pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Tráfego Inesperado para Porto Standard | O trânsito foi detetado num dispositivo utilizando uma porta reservada a outro protocolo. | Maior |

## <a name="protocol-violation-engine-alerts"></a>Alertas de motor de violação de protocolo

| Título | Descrição | Gravidade |
|--|--|--|
| Pacotes malformados excessivos em uma única sessão | Um número anormal de pacotes mal formados enviados do dispositivo de origem para o dispositivo de destino. Isto pode indicar comunicações erróneas, ou uma tentativa de manipular o dispositivo direcionado. | Maior |
| Atualização de Firmware | Um dispositivo de origem enviou um comando para atualizar o firmware num dispositivo de destino. Verifique se as recentes atualizações de programação, configuração e firmware es feitas para o dispositivo de destino são válidas. | Aviso |
| Código de função não suportado por Outstation | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Mensagem ILEGAL BACNet | O dispositivo de origem iniciou um pedido inválido. | Maior |
| Tentativa ilegal de ligação no porto 0 | Um dispositivo de origem tentou ligar-se ao dispositivo de destino na porta nº 0 (0). Para a TCP, a porta 0 está reservada e não pode ser utilizada. Para a UDP, a porta é opcional e um valor de 0 significa que não há porta. Normalmente não há serviço num sistema que ouve no porto 0. Este evento pode indicar uma tentativa de atacar o dispositivo de destino ou indicar que uma aplicação foi programada incorretamente. | Menor |
| Operação ilegal de DNP3 | O dispositivo de origem iniciou um pedido inválido. | Maior |
| Operação ilegal do MODBUS (Exceção levantada pelo mestre) | O dispositivo de origem iniciou um pedido inválido. | Maior |
| Operação ILEGAL MODBUS (Código de Função Zero) | O dispositivo de origem iniciou um pedido inválido. | Maior |
| Versão de protocolo ilegal | O dispositivo de origem iniciou um pedido inválido. | Maior |
| Parâmetro incorreto enviado para a estação | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Início de um Código de Função Obsoleto (Dados inicializos) | O dispositivo de origem iniciou um pedido inválido. | Menor |
| Início de um Código de Função Obsoleto (Save Config) | O dispositivo de origem iniciou um pedido inválido. | Menor |
| Mestre solicitou uma confirmação da camada de aplicação | O dispositivo de origem iniciou um pedido inválido. | Aviso |
| Exceção Modbus | Um dispositivo de origem (escravo) devolveu uma exceção a um dispositivo de destino (mestre). | Maior |
| Dispositivo escravo recebeu tipo ASDU ilegal | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Dispositivo escravo recebeu comando ilegal causa de transmissão | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Dispositivo escravo recebeu endereço comum ilegal | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Dispositivo escravo recebeu parâmetro de endereço de dados ilegais | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Dispositivo escravo recebeu parâmetro de valor de dados ilegal | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Dispositivo escravo recebeu código de função ilegal | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Dispositivo escravo recebeu endereço de objeto de informação ilegal | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Objeto desconhecido enviado para a outstation | O dispositivo de destino recebeu um pedido inválido. | Maior |
| Utilização de um Código de Função Reservado | O dispositivo de origem iniciou um pedido inválido. | Maior |
| Utilização de Formatação Imprópria por Outstation | O dispositivo de origem iniciou um pedido inválido. | Aviso |
| Utilização de Bandeiras de Estado Reservado (IIN) | Um dispositivo de origem DNP3 (outstation) utilizou o Indicador Interno Reservado 2.6. É aconselhável verificar a configuração do dispositivo. | Aviso |

## <a name="malware-engine-alerts"></a>Alertas de motor de malware

| Título | Descrição| Gravidade |
|--|--|--|
| Tentativa de Ligação a IP malicioso conhecido | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Mensagem SMB inválida (implante doublePulsar Backdoor) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Pedido de nome de domínio malicioso | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Ficheiro de teste de malware detetado - Sucesso DO EICAR AV | Foi detetado um ficheiro de teste EICAR AV no tráfego entre dois dispositivos. O ficheiro não é malware. É utilizado para confirmar que o software antivírus está instalado corretamente; demonstrar o que acontece quando um vírus é encontrado, e verificar procedimentos internos e reações quando um vírus é encontrado. O software antivírus deve detetar o EICAR como se fosse um vírus real. | Maior |
| Suspeita de Malware Conficker | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Suspeita de negação de ataque de serviço | Um dispositivo de origem tentou iniciar um número excessivo de novas ligações a um dispositivo de destino. Isto pode ser um ataque de Negação de Serviço (DOS) contra o dispositivo de destino, e pode interromper a funcionalidade do dispositivo, o desempenho do impacto e a disponibilidade do serviço, ou causar erros irrecuperáveis. | Crítico |
| Suspeita de Atividade Maliciosa | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Suspeita de atividade maliciosa (BlackEnergy) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de atividade maliciosa (DarkComet) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de atividade maliciosa (Duqu) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de Atividade Maliciosa (Chama) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de atividade maliciosa (Havex) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de atividade maliciosa (Karagany) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de atividade maliciosa (LightsOut) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de atividade maliciosa (Nome consultas) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Suspeita de atividade maliciosa (hera venenosa) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de atividade maliciosa (Regin) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de atividade maliciosa (Stuxnet) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de Atividade Maliciosa (WannaCry) | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Suspeita de Malware NãoPetya - Parâmetros Ilegais de SMB detetados | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de Malware NotPetya - Transação Ilegal de SMB Detetada | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |
| Suspeita de execução remota de código com o PsExec | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Suspeita de gestão remota de serviços windows | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Arquivo executável suspeito detetado no ponto final | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Maior |
| Tráfego Suspeito Detetado | Foi detetada atividade suspeita da rede. Esta atividade pode estar associada a um ataque explorando um método usado por malware conhecido. | Crítico |

## <a name="operational-engine-alerts"></a>Alertas de motor operacional

| Título | Descrição | Gravidade |
|--|--|--|
| Um comando S7 Stop PLC foi enviado | O dispositivo de origem enviou um comando de paragem para um controlador de destino. O controlador deixará de funcionar até que seja enviado um comando inicial. | Aviso |
| Operação BACNet falhou | Um servidor devolveu um código de erro. Isto indica um erro do servidor ou um pedido inválido por parte de um cliente. | Maior |
| Mau estado do dispositivo MMS | Um dispositivo de fabrico virtual MMS (VMD) enviou uma mensagem de estado. A mensagem indica que o servidor pode não estar configurado corretamente, parcialmente operacional ou não operacional. | Maior |
| Alteração da configuração do dispositivo | Foi detetada uma alteração de configuração num dispositivo de origem. | Menor |
| Transbordo de tampão de evento contínuo na outstation | Um evento de transbordo de tampão foi detetado num dispositivo de origem. O evento pode causar corrupção de dados, falhas de programas ou execução de código malicioso. | Maior |
| Reset do controlador | Um dispositivo de origem enviou um comando de reset para um controlador de destino. O controlador parou de funcionar temporariamente e recomeçou automaticamente. | Aviso |
| Paragem do Controlador | O dispositivo de origem enviou um comando de paragem para um controlador de destino. O controlador deixará de funcionar até que seja enviado um comando inicial. | Aviso |
| Dispositivo não recebeu um endereço IP dinâmico | O dispositivo de origem está configurado para receber um endereço IP dinâmico de um servidor DHCP, mas não recebeu um endereço. Isto indica um erro de configuração no dispositivo ou um erro operacional no servidor DHCP. Recomenda-se notificar o administrador da rede do incidente | Maior |
| Suspeita-se que o dispositivo esteja desligado (sem resposta) | Um dispositivo de origem não respondeu a um comando enviado para ele. Pode ter sido desligado quando o comando foi enviado. | Maior |
| Pedido de serviço EtherNet/IP CIP falhou | Um servidor devolveu um código de erro. Isto indica um erro do servidor ou um pedido inválido por parte de um cliente. | Maior |
| O comando do protocolo de encapsulação EtherNet/IP falhou | Um servidor devolveu um código de erro. Isto indica um erro do servidor ou um pedido inválido por parte de um cliente. | Maior |
| Transbordo de tampão de evento na outstation | Um evento de transbordo de tampão foi detetado num dispositivo de origem. O evento pode causar corrupção de dados, falhas de programas ou execução de código malicioso. | Maior |
| Operação de backup esperada não ocorreu | A atividade prevista de transferência de cópias de segurança/ficheiro não ocorreu entre dois dispositivos. Isto pode indicar erros no processo de transferência de ficheiros de backup/ficheiro. | Maior |
| Falha no comando GE SRTP | Um servidor devolveu um código de erro. Isto indica um erro do servidor ou um pedido inválido por parte de um cliente. | Maior |
| Ge SRTP Stop PLC Comando foi enviado | O dispositivo de origem enviou um comando de paragem para um controlador de destino. O controlador deixará de funcionar até que seja enviado um comando inicial. | Aviso |
| O bloco de controlo GOOSE requer uma configuração adicional | Um dispositivo de origem enviou uma mensagem GOOSE indicando que o dispositivo precisa de ser encomendado. Isto significa que o bloco de controlo GOOSE requer uma configuração adicional e as mensagens GOOSE são parcial ou completamente não operacionais. | Maior |
| A configuração do conjunto de dados goose foi alterada | Um conjunto de dados de mensagem (identificado pelo ID do protocolo) foi alterado num dispositivo de origem. Isto significa que o dispositivo irá reportar um conjunto de dados diferente para esta mensagem. | Aviso |
| Estatuto inesperado do controlador honeywell | Um controlador Honeywell enviou uma mensagem de diagnóstico inesperada indicando uma mudança de estado. | Aviso |
| ERRO DO Cliente HTTP | O dispositivo de origem iniciou um pedido inválido. | Aviso |
| Endereço IP ilegal | O sistema detetou o tráfego entre um dispositivo de origem e um endereço IP que é um endereço inválido. Isto pode indicar uma configuração errada ou uma tentativa de gerar tráfego ilegal. | Menor |
| Erro de autenticação Master-Slave | O processo de autenticação entre um dispositivo de origem DNP3 (master) e um dispositivo de destino (outstation) falhou. | Menor |
| Pedido de serviço MMS falhado | Um servidor devolveu um código de erro. Isto indica um erro do servidor ou um pedido inválido por parte de um cliente. | Maior |
| Sem tráfego detetado na interface do sensor | Um sensor parou de detetar o tráfego de rede numa interface de rede. | Crítico |
| Servidor UA OPC levantou um evento que requer a atenção do utilizador | Um servidor UA da OPC enviou uma notificação de evento a um cliente. Este tipo de evento requer a atenção do utilizador | Maior |
| Pedido de serviço da UA da OPC falhou | Um servidor devolveu um código de erro. Isto indica um erro do servidor ou um pedido inválido por parte de um cliente. | Maior |
| Outstation Reiniciado | Foi detetado um recomeço a frio num dispositivo de origem. Isto significa que o dispositivo foi fisicamente desligado e ligado novamente. | Aviso |
| Outstation Restarts Frequentemente | Um número excessivo de recomeces a frio foi detetado num dispositivo de origem. Isto significa que o dispositivo foi fisicamente desligado e ligado novamente um número excessivo de vezes. | Menor |
| Configuração da Outstation alterada | Foi detetada uma alteração de configuração num dispositivo de origem. | Maior |
| Configuração corrupta da Outstation detetada | Este dispositivo de origem DNP3 (outstation) relatou uma configuração corrompida. | Maior |
| Comando Profinet DCP falhou | Um servidor devolveu um código de erro. Isto indica um erro do servidor ou um pedido inválido por parte de um cliente. | Maior |
| Reset da fábrica de dispositivos Profinet | Um dispositivo de origem enviou um comando de reset de fábrica para um dispositivo de destino Profinet. O comando reset limpa as configurações do dispositivo Profinet e interrompe o seu funcionamento. | Aviso |
| Operação RPC falhou | Um servidor devolveu um código de erro. Isto indica um erro do servidor ou um pedido inválido por parte de um cliente. | Maior |
| A configuração do conjunto de dados de mensagens de valores amostrados foi alterada | Um conjunto de dados de mensagem (identificado pelo ID do protocolo) foi alterado num dispositivo de origem. Isto significa que o dispositivo irá reportar um conjunto de dados diferente para esta mensagem. | Aviso |
| Falha irrecuperável do dispositivo escravo | Foi detetado um erro de condição irrecuperável num dispositivo de origem. Este tipo de erro geralmente indica uma falha de hardware ou falha na realização de um comando específico. | Maior |
| Suspeita de problemas de hardware na outstation | Foi detetado um erro de condição irrecuperável num dispositivo de origem. Este tipo de erro geralmente indica uma falha de hardware ou falha na realização de um comando específico. | Maior |
| Suspeita de dispositivo MODBUS sem resposta | Um dispositivo de origem não respondeu a um comando enviado para ele. Pode ter sido desligado quando o comando foi enviado. | Menor |
| Tráfego detetado na Interface do Sensor | Um sensor retomou a deteção do tráfego de rede numa interface de rede. | Aviso |

## <a name="next-steps"></a>Passos seguintes

Pode [Gerir eventos de alerta.](how-to-manage-the-alert-event.md)
Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
