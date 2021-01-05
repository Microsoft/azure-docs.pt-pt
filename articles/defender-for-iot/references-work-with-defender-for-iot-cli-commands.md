---
title: Trabalhar com o Defender para comandos IoT CLI
description: Este artigo descreve os comandos Defender para IoT CLI para sensores e consolas de gestão no local.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 48c525004b095c1e0f498f86a5395d0002be26f1
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845247"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Trabalhar com o Defender para comandos IoT CLI

Este artigo descreve comandos CLI para sensores e consolas de gestão no local. Os comandos são acessíveis a administradores, utilizadores de ciberx e utilizadores de suporte.

Defina regras de exclusão quando estiver a planear atividades de manutenção ou uma atividade que não exija um alerta.

## <a name="create-local-alert-exclusion-rules"></a>Criar regras locais de exclusão de alerta

Pode criar uma regra de exclusão introduzindo o seguinte comando no CLI:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Os atributos que pode definir dentro das regras de exclusão de alerta são os seguintes:

| Atributo | Descrição |
|--|--|
| [-h] | Imprime a informação de ajuda para o comando. |
| -n NOME | O nome da regra que está a ser criada. |
| [-ts TIMES] | O tempo para o qual a regra está ativa. Isto deve ser especificado como:<br />`xx:yy-xx:yy`<br />Pode definir mais de um período de tempo usando uma vírgula entre eles. Por exemplo: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-dir DIREÇÃO] | A direção em que a regra é aplicada. Isto deve ser especificado como:<br />`both | src | dst` |
| [-dev DEVICES] | O endereço IP e o tipo de endereço dos dispositivos a excluir pela regra, especificado como:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a ALERTAS] | O nome do alerta que a regra excluirá:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Apêndice regras locais de exclusão de alerta

Pode adicionar novas regras às atuais regras de exclusão de alerta, introduzindo o seguinte comando no CLI:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Os atributos aqui utilizados são semelhantes aos atributos descritos quando está a criar regras locais de exclusão de alerta. No uso aqui, os atributos são aplicados às regras existentes.

## <a name="show-local-alert-exclusion-rules"></a>Mostrar regras locais de exclusão de alerta

Introduza o seguinte comando para visualizar todas as regras de exclusão existentes:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Eliminar as regras locais de exclusão de alerta

Pode eliminar uma regra de exclusão de alerta existente introduzindo o seguinte comando:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Pode utilizar o seguinte atributo com as regras de exclusão de alerta:

| Atributo | Descrição|
| --------- | ---------------------------------- |
| -n NOME | O nome da regra a ser apagado. |

## <a name="sync-time-from-the-ntp-server"></a>Tempo de sincronização a partir do servidor NTP

Pode ativar e desativar uma sincronização de tempo a partir de um servidor NTP.

### <a name="enable-ntp-sync"></a>Ativar a sincronização ntp

A introdução do seguinte comando permitirá uma recuperação periódica do tempo atual a partir de um servidor NTP especificado:

```azurecli-interactive
ntp enable IP
```

O atributo que pode definir dentro do comando é o endereço IP do servidor NTP.

### <a name="disable-ntp-sync"></a>Desativar a sincronização ntp

A introdução do seguinte comando irá desativar a sincronização de tempo com o servidor NTP especificado:

```azurecli-interactive
ntp disable IP
```

O atributo que pode definir dentro do comando é o endereço IP do servidor NTP.

## <a name="configure-the-network"></a>Configurar a rede

A tabela a seguir descreve os comandos disponíveis para configurar as suas opções de rede para Azure Defender para IoT:

|Nome|Comando|Descrição|
|-----------|-------|-----------|
|Ping|`ping IP `| Os endereços de pings fora da plataforma Defender for IoT.|
|Blink|`network blink`|Permite alterar os parâmetros de configuração da rede.|
|Reconfigurar a rede |`network edit-settings`| Permite alterar os parâmetros de configuração da rede. |
|Mostrar definições de rede |`network list`|Exibe os parâmetros do adaptador de rede. |
|Validar a configuração da rede |`network validate` |Apresenta as definições da rede de saída. <br /> <br />Por exemplo: <br /> <br />Definições de rede atuais: <br /> interface: eth0 <br /> ip: 10.100.100.1 <br />sub-rede: 255.255.255.0 <br />porta de entrada padrão: 10.100.100.254 <br />dns: 10.100.100.254 <br />interfaces monitor: eth1|
|Importar um certificado |`certificate import FILE` |Importa o certificado HTTPS. Terá de especificar o caminho completo, o que leva a um \* ficheiro .crt. |
|Mostre a data |`date` |Devolve a data atual no anfitrião em formato GMT. |

## <a name="filter-network-configurations"></a>Configurações de rede de filtros

O `network capture-filter` comando permite que os administradores eliminem o tráfego de rede que não precisa de ser analisado. Filtrar o tráfego utilizando uma lista de incluí-lo ou uma lista de exclusão.

```azurecli-interactive
network capture-filter
```

Depois de entrar no comando, será solicitado com a seguinte pergunta:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Selecione `Y` para abrir um ficheiro nano onde pode adicionar dispositivos, canais, portas e subconjuntos de acordo com a seguinte sintaxe:

| Atributo | Descrição |
|--|--|
| 1.1.1.1 | Inclui todo o tráfego para este dispositivo. |
| 1.1.1.1,2.2.2.2 | Inclui todo o tráfego para este canal. |
| 1.1.1,2.2.2 | Inclui todo o tráfego para esta sub-rede. |

Argumentos separados, deixando cair uma linha.

Quando se inclui um dispositivo, um canal ou uma sub-rede, o sensor processa todo o tráfego válido para esse argumento, incluindo portas e tráfego que normalmente não seriam processados.

Em seguida, ser-lhe-á perguntado o seguinte:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Selecione `Y` para abrir um ficheiro nano onde pode adicionar dispositivo, canais, portas e subconjuntos de acordo com a seguinte sintaxe:

| Atributo | Descrição |
|--|--|
| 1.1.1.1 | Exclui todo o tráfego deste dispositivo. |
| 1.1.1.1,2.2.2.2 | Exclui todo o tráfego deste canal, ou seja, todo o tráfego entre dois dispositivos. |
| 1.1.1.1,2.2.2.2,443 | Exclui todo o tráfego deste canal por porta. |
| 1.1.1 | Exclui todo o tráfego desta sub-rede. |
| 1.1.1,2.2.2 | Exclui todo o tráfego entre sub-redes. |

Argumentos separados, deixando cair uma linha.

Quando excluir um dispositivo, um canal ou uma sub-rede, o sensor excluirá todo o tráfego válido para esse argumento.

### <a name="ports"></a>Portas

Incluir ou excluir portas UDP e TCP para todo o tráfego.

>`502`: porto único

>`502,443`: ambas as portas

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Componentes

Perguntam-lhe o seguinte:

>`In which component do you wish to apply this capture filter?`

As suas opções são:  `all` `dissector` , ou `collector` `statistics-collector` `rpc-parser` `smb-parser` .

Na maioria dos casos de utilização, selecione `all` .

### <a name="custom-base-capture-filter"></a>Filtro de captura de base personalizado

O filtro de captura de base é a linha de base para os componentes. Por exemplo, o filtro determina quais as portas disponíveis para o componente.

Selecione `Y` para todas as seguintes opções. Todos os filtros são adicionados à linha de base após a definição das alterações. Se fizer uma alteração, substituirá a linha de base existente.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Se optar por excluir uma sub-rede como 1.1.1:

- `internal` excluirá apenas essa sub-rede.

- `all-connected` excluirá essa sub-rede e todo o tráfego de e para essa sub-rede.

Recomendamos que selecione `internal` .

> [!NOTE]
> As suas escolhas são usadas para todos os filtros da ferramenta e não dependem da sessão. Por outras palavras, nunca se pode escolher `internal`   alguns filtros e  `all-connected`   outros.

### <a name="comments"></a>Comentários

Pode ver filtros em  ```/var/cyberx/properties/cybershark.properties``` :

- **estatística-colector:**  `bpf_filter property` em ```/var/cyberx/properties/net.stats.collector.properties```

- **dissector**: `override.capture_filter`   propriedade em ```/var/cyberx/properties/cybershark.properties```

- **rpc-parser**: `override.capture_filter` propriedade em ```/var/cyberx/properties/rpc-parser.properties```

- **smb-parser**:  `override.capture_filter`   propriedade em ```/var/cyberx/properties/smb-parser.properties```

- **coletor**: `general.bpf_filter`   propriedade em ```/var/cyberx/properties/collector.properties```

Pode restaurar a configuração predefinida introduzindo o seguinte código para o utilizador cyberx:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Definir anfitriões de clientes e servidores

Se o Defender for IoT não detetar automaticamente os anfitriões do cliente e do servidor, insira o seguinte comando para definir os anfitriões do cliente e do servidor:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Pode utilizar os seguintes atributos com o `directions` comando:

| Atributo | Descrição |
|--|--|
| [-h] | As impressões ajudam a informação para o comando. |
| [-identificador identificador] | O identificador do servidor. |
| [-porto PORTO] | A porta do servidor. |
| [--remover] | Remove um cliente ou servidor da lista. |
| [--adicionar] | Adiciona um cliente ou anfitrião do servidor à lista. |
| [--tcp] | Utilize o TCP quando comunicar com este hospedeiro. |
| [-udp] | Utilize uDP ao comunicar com este hospedeiro. |

## <a name="system-actions"></a>Ações do sistema
A tabela a seguir descreve os comandos disponíveis para executar várias ações do sistema dentro do Defender para IoT:

|Nome|Código|Description|
|----|----|-----------|
|Reinicie o anfitrião|`system reboot`|Reinicia o dispositivo de anfitrião.|
|Desligue o anfitrião|`system shutdown`|Desliga o hospedeiro.|
|Apoiar o sistema|`system backup`|Inicia uma cópia de segurança imediata (uma cópia de segurança não programada).|
|Restaurar o sistema a partir de uma cópia de segurança|`system restore`|Restauros do mais recente backup.|
|Listar os ficheiros de backup|`system backup-list`|Lista os ficheiros de cópia de segurança disponíveis.|
|Mostrar o estado de todos os serviços da plataforma Defender para IoT|`system sanity`|Verifica o desempenho do sistema listando o estado atual de todos os serviços da plataforma Defender para ioT.|
|Mostrar a versão de software|`system version`|Exibe a versão do software atualmente em execução no sistema.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>Implementar certificados SSL e TLS para aparelhos

Introduza o seguinte comando para importar certificados empresariais SSL e TLS no CLI:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Para utilizar a ferramenta, é necessário fazer o upload dos ficheiros de certificados para o dispositivo. Pode fazê-lo através de ferramentas como WinSCP ou Wget. 

O comando suporta as seguintes bandeiras de entrada:

| Sinalizador | Descrição |
|--|--|
| -h | Mostra a sintaxe de ajuda da linha de comando. |
| --crt | O caminho para o ficheiro de certificado (extensão.crt). |
| -chave | O \* ficheiro .key. O comprimento da chave deve ser no mínimo 2.048 bits. |
| ...cadeia | Caminho para o arquivo da cadeia de certificados (opcional). |
| --passe | Frase-passe usada para encriptar o certificado (opcional). |
| --passphrase-set | O padrão é **falso,** **não é desuso**. <br />Definir para **True** para utilizar a frase de passe anterior fornecida com o certificado anterior (opcional). |  |

Quando estiver a utilizar a ferramenta:

- Verifique se os ficheiros do certificado são legíveis no aparelho. 

- Confirme com o domínio do aparelho (tal como aparece no certificado) com o seu servidor DNS e o endereço IP correspondente. 
    
## <a name="see-also"></a>Veja também

[Defender para apis sensores e consolas de gestão IoT](references-work-with-defender-for-iot-apis.md)
