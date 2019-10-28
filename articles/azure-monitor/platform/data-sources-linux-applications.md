---
title: Coletar desempenho do aplicativo Linux no Azure Monitor | Microsoft Docs
description: Este artigo fornece detalhes sobre como configurar o agente de Log Analytics para Linux para coletar contadores de desempenho para MySQL e Apache HTTP Server.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 05/04/2017
ms.openlocfilehash: 60f09035f4aabcbd6348fb5608b812ca4b001b45
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932459"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Coletar contadores de desempenho para aplicativos do Linux no Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Este artigo fornece detalhes sobre como configurar o [agente de log Analytics para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) para coletar contadores de desempenho para aplicativos específicos no Azure monitor.  Os aplicativos incluídos neste artigo são:  

- [MySQL](#mysql)
- [Servidor HTTP Apache](#apache-http-server)

## <a name="mysql"></a>MySQL
Se o servidor MySQL ou o servidor MariaDB for detectado no computador quando o agente de Log Analytics estiver instalado, um provedor de monitoramento de desempenho para o servidor MySQL será instalado automaticamente. Esse provedor se conecta ao servidor MySQL/MariaDB local para expor as estatísticas de desempenho. As credenciais de usuário do MySQL devem ser configuradas para que o provedor possa acessar o servidor MySQL.

### <a name="configure-mysql-credentials"></a>Configurar credenciais do MySQL
O provedor de OMI do MySQL requer um usuário do MySQL pré-configurado e as bibliotecas de cliente do MySQL instaladas para consultar as informações de desempenho e integridade da instância do MySQL.  Essas credenciais são armazenadas em um arquivo de autenticação armazenado no agente do Linux.  O arquivo de autenticação especifica qual endereço de ligação e porta a instância do MySQL está escutando e quais credenciais usar para coletar métricas.  

Durante a instalação do agente de Log Analytics para Linux, o provedor de OMI do MySQL examinará os arquivos de configuração do MySQL My. cnf (locais padrão) para o endereço de ligação e a porta e definirá parcialmente o arquivo de autenticação do OMI do MySQL.

O arquivo de autenticação do MySQL é armazenado em `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formato do arquivo de autenticação
Este é o formato para o arquivo de autenticação OMI do MySQL

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

As entradas no arquivo de autenticação são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Porta | Representa a porta atual na qual a instância do MySQL está escutando. A porta 0 especifica que as propriedades a seguir são usadas para a instância padrão. |
| Endereço de associação| Endereço de associação do MySQL atual. |
| o nome de utilizador| Usuário do MySQL usado para monitorar a instância do MySQL Server. |
| Senha codificada em base64| Senha do usuário de monitoramento do MySQL codificada em base64. |
| AutoUpdate| Especifica se é para examinar novamente as alterações no arquivo My. cnf e substituir o arquivo de autenticação OMI do MySQL quando o provedor de OMI do MySQL for atualizado. |

### <a name="default-instance"></a>Instância padrão
O arquivo de autenticação OMI do MySQL pode definir uma instância padrão e um número da porta para facilitar o gerenciamento de várias instâncias do MySQL em um host Linux.  A instância padrão é denotada por uma instância com a porta 0. Todas as instâncias adicionais herdarão as propriedades definidas da instância padrão, a menos que especifiquem valores diferentes. Por exemplo, se a instância do MySQL ouvindo na porta ' 3308 ' for adicionada, o endereço de ligação, o nome de usuário e a senha codificada em base64 da instância padrão serão usados para tentar monitorar a instância escutando em 3308. Se a instância em 3308 estiver associada a outro endereço e usar o mesmo nome de usuário e senha do MySQL, somente o endereço de ligação será necessário e as outras propriedades serão herdadas.

A tabela a seguir tem configurações de instância de exemplo 

| Descrição | Ficheiros |
|:--|:--|
| Instância padrão e instância com a porta 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Instância padrão e instância com a porta 3308 e nome de usuário e senha diferentes. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programa de arquivo de autenticação do MySQL OMI
Incluído na instalação do provedor de OMI do MySQL está um programa de arquivo de autenticação do MySQL OMI que pode ser usado para editar o arquivo de autenticação OMI do MySQL. O programa de arquivo de autenticação pode ser encontrado no local a seguir.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> O arquivo de credenciais deve ser legível pela conta omsagent. É recomendável executar o comando mycimprovauth como omsgent.

A tabela a seguir fornece detalhes sobre a sintaxe para usar o mycimprovauth.

| Operação | Exemplo | Descrição
|:--|:--|:--|
| atualização automática *falsa ou verdadeira* | mycimprovauth AutoUpdate false | Define se o arquivo de autenticação será atualizado ou não automaticamente na reinicialização ou atualização. |
| *senha de nome de usuário do endereço de ligação* padrão | mycimprovauth padrão 127.0.0.1 root pwd | Define a instância padrão no arquivo de autenticação OMI do MySQL.<br>O campo de senha deve ser inserido em texto sem formatação-a senha no arquivo de autenticação OMI do MySQL será codificada em base 64. |
| excluir *padrão ou port_num* | mycimprovauth 3308 | Exclui a instância especificada por padrão ou por número de porta. |
| Ajuda | ajuda do mycimprov | Imprime uma lista de comandos a serem usados. |
| Imprimir | mycimprov imprimir | Imprime um arquivo de autenticação de OMI do MySQL fácil de ler. |
| atualizar *a senha do nome de usuário do endereço de ligação* do port_num | atualização do mycimprov 3307 127.0.0.1 raiz pwd | Atualiza a instância especificada ou adiciona a instância, caso ela não exista. |

Os comandos de exemplo a seguir definem uma conta de usuário padrão para o servidor MySQL no localhost.  O campo de senha deve ser inserido em texto sem formatação-a senha no arquivo de autenticação OMI do MySQL será codificada em base 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Permissões de banco de dados necessárias para contadores de desempenho do MySQL
O usuário do MySQL requer acesso às consultas a seguir para coletar dados de desempenho do servidor MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


O usuário do MySQL também requer acesso de seleção às tabelas padrão a seguir.

- information_schema
- MySQL. 

Esses privilégios podem ser concedidos executando os seguintes comandos Grant.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Para conceder permissões a um usuário de monitoramento do MySQL, o usuário de concessão deve ter o privilégio ' GRANT OPTION ', bem como o privilégio que está sendo concedido.

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Depois de configurar o agente de Log Analytics para Linux para enviar dados para Azure Monitor, você deve configurar os contadores de desempenho a serem coletados.  Use o procedimento em [fontes de dados de desempenho do Windows e do Linux no Azure monitor](data-sources-performance-counters.md) com os contadores na tabela a seguir.

| Nome do objeto | Nome do contador |
|:--|:--|
| Base de Dados MySQL | Espaço em disco em bytes |
| Base de Dados MySQL | Tabelas |
| Servidor MySQL | Porcentagem de conexão anulada |
| Servidor MySQL | PCT de uso de conexão |
| Servidor MySQL | Uso de espaço em disco em bytes |
| Servidor MySQL | PCT de verificação de tabela completa |
| Servidor MySQL | Percentual de ocorrência do pool de buffers InnoDB |
| Servidor MySQL | % De uso do pool de buffers InnoDB |
| Servidor MySQL | % De uso do pool de buffers InnoDB |
| Servidor MySQL | PCT de ocorrência de cache de chave |
| Servidor MySQL | PCT de uso do cache de chaves |
| Servidor MySQL | PCT de gravação do cache de chaves |
| Servidor MySQL | PCT de ocorrência do cache de consulta |
| Servidor MySQL | PCT de removidas de cache de consulta |
| Servidor MySQL | PCT de uso do cache de consulta |
| Servidor MySQL | PCT de ocorrência do cache de tabela |
| Servidor MySQL | PCT de uso de cache de tabela |
| Servidor MySQL | PCT de contenção de bloqueio de tabela |

## <a name="apache-http-server"></a>Servidor HTTP Apache 
Se o Apache HTTP Server for detectado no computador quando o pacote omsagent estiver instalado, um provedor de monitoramento de desempenho para o Apache HTTP Server será instalado automaticamente. Esse provedor se baseia em um módulo do Apache que deve ser carregado no servidor HTTP do Apache para acessar dados de desempenho. O módulo pode ser carregado com o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para descarregar o módulo de monitoramento do Apache, execute o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Depois de configurar o agente de Log Analytics para Linux para enviar dados para Azure Monitor, você deve configurar os contadores de desempenho a serem coletados.  Use o procedimento em [fontes de dados de desempenho do Windows e do Linux no Azure monitor](data-sources-performance-counters.md) com os contadores na tabela a seguir.

| Nome do objeto | Nome do contador |
|:--|:--|
| Servidor HTTP Apache | Trabalhadores ocupados |
| Servidor HTTP Apache | Trabalhos ociosos |
| Servidor HTTP Apache | Porcentagem de trabalhadores ocupados |
| Servidor HTTP Apache | Percentual total de CPU |
| Host virtual Apache | Erros por minuto – cliente |
| Host virtual Apache | Erros por minuto-servidor |
| Host virtual Apache | KB por solicitação |
| Host virtual Apache | Solicitações em KB por segundo |
| Host virtual Apache | Solicitações por segundo |



## <a name="next-steps"></a>Passos seguintes
* [Coletar contadores de desempenho](data-sources-performance-counters.md) de agentes do Linux.
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 