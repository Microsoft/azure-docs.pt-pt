---
title: Recolher o desempenho de aplicações do Linux no Azure Monitor | Documentos da Microsoft
description: Este artigo fornece detalhes para configurar o agente do Log Analytics para Linux recolher contadores de desempenho para o MySQL e Apache HTTP Server.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ea74440a5c8a9a2584e742ec72ccf888b6bb5ad9
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621537"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Recolher contadores de desempenho de aplicações do Linux no Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Este artigo fornece detalhes para configurar o [agente do Log Analytics para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) recolher contadores de desempenho de aplicações específicas para o Azure Monitor.  Os aplicativos incluídos neste artigo são:  

- [MySQL](#mysql)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Se o servidor MySQL ou servidor MariaDB é detetado no computador quando é instalado o agente Log Analytics, um fornecedor para o servidor MySQL de monitorização de desempenho será instalado automaticamente. Este fornecedor estabelece ligação ao servidor MySQL/MariaDB local para expor as estatísticas de desempenho. As credenciais de utilizador MySQL tem de ser configuradas para que o fornecedor possam acessar o servidor MySQL.

### <a name="configure-mysql-credentials"></a>Configurar as credenciais do MySQL
O fornecedor de MySQL OMI exige que o usuário MySQL pré-configurado e instalado bibliotecas de cliente do MySQL para consultar o desempenho e informações de estado de funcionamento da instância do MySQL.  Estas credenciais são armazenadas num arquivo de autenticação que é armazenado no agente Linux.  O ficheiro de autenticação especifica o endereço de enlace e a porta a instância do MySQL está a escutar e quais as credenciais a utilizar para reunir as métricas.  

Durante a instalação do agente do Log Analytics para Linux o OMI MySQL fornecedor irá analisar MySQL cnf de ficheiros de configuração (localizações predefinidas) para o endereço de enlace e a porta e parcialmente definir o ficheiro de autenticação do MySQL OMI.

O ficheiro de autenticação do MySQL é armazenado em `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formato de ficheiro de autenticação
Segue-se o formato do ficheiro de autenticação do MySQL OMI

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

As entradas no ficheiro de autenticação estão descritas na tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Porta | Representa a porta atual que está a escutar a instância do MySQL. Porta 0 Especifica que as propriedades seguintes são utilizadas para a instância predefinida. |
| Endereço de enlace| Endereço-atual MySQL bind. |
| o nome de utilizador| Utilizador MySQL utilizado para utilizar para monitorizar a instância do servidor MySQL. |
| Palavra-passe de codificada em Base64| Palavra-passe do utilizador de monitorização MySQL codificado em Base64. |
| AutoUpdate| Especifica se pretende reanalisar para alterações no arquivo cnf e substituir o ficheiro de autenticação do MySQL OMI quando o fornecedor do MySQL OMI é atualizado. |

### <a name="default-instance"></a>Instância predefinida
O ficheiro de autenticação do MySQL OMI pode definir um número de instância e a porta predefinida para facilitar o gerenciamento de várias instâncias de MySQL num anfitrião Linux mais fácil.  A instância predefinida está em falta por uma instância com a porta 0. Todas as instâncias adicionais herdará as propriedades definidas na instância predefinida, a menos que especificam valores diferentes. Por exemplo, se for adicionada a instância do MySQL à escuta na porta '3308', endereço de enlace a instância predefinida, nome de utilizador e palavra-passe de codificada em Base64 serão utilizados para experimentar e monitorizar a instância que ouça 3308. Se a instância no 3308 está vinculada a outro endereço e utiliza o mesmo par de nome de utilizador e palavra-passe de MySQL não é necessário apenas o enlace de endereço e as outras propriedades serão herdadas.

A tabela seguinte inclui as definições de instância de exemplo 

| Descrição | Ficheiro |
|:--|:--|
| Instância predefinida e instância com a porta 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Instância predefinida e instância com porta 3308 e outro nome de utilizador e palavra-passe. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programa de ficheiro de autenticação do MySQL OMI
Incluído com a instalação do fornecedor MySQL OMI é um programa de ficheiro de autenticação de MySQL OMI que pode ser usado para editar o ficheiro de autenticação do MySQL OMI. O programa de ficheiro de autenticação pode ser encontrado na seguinte localização.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> O ficheiro de credenciais tem de ser legível pela conta omsagent. Executar o comando de mycimprovauth como omsgent é recomendado.

A tabela seguinte fornece detalhes sobre a sintaxe para a utilização de mycimprovauth.

| Operação | Exemplo | Descrição
|:--|:--|:--|
| AutoUpdate *falsa ou verdadeira* | mycimprovauth autoupdate false | Define o ficheiro de autenticação ou não será atualizado automaticamente no reiniciar ou atualizar. |
| predefinição *palavra-passe do endereço de enlace de nome de utilizador* | mycimprovauth padrão 127.0.0.1 raiz pwd | Define a instância predefinida no OMI MySQL de ficheiro de autenticação.<br>O campo de palavra-passe deve ser introduzido no texto sem formatação – a palavra-passe no ficheiro de autenticação de OMI do MySQL estará Base 64 codificada. |
| eliminar *predefinido ou port_num* | mycimprovauth 3308 | Elimina a instância especificada por qualquer um dos padrão ou por número de porta. |
| ajuda | ajuda de mycimprov | Mostra uma lista de comandos a utilizar. |
| Impressão | mycimprov impressão | Mostra uma fácil ler MySQL OMI o ficheiro de autenticação. |
| Atualizar port_num *palavra-passe do endereço de enlace de nome de utilizador* | mycimprov atualização 3307 127.0.0.1 raiz pwd | Atualiza a instância especificada ou adiciona a instância, se não existir. |

Os comandos de exemplo seguintes definem uma conta de usuário padrão para o servidor MySQL no localhost.  O campo de palavra-passe deve ser introduzido no texto sem formatação – a palavra-passe no ficheiro de autenticação de OMI do MySQL estará Base 64 codificada

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Permissões de base de dados necessárias para contadores de desempenho do MySQL
O utilizador MySQL necessita de acesso para as consultas seguintes para recolher dados de desempenho do servidor MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


O utilizador MySQL também requer acesso SELECT para as seguintes tabelas de predefinição.

- information_schema
- mysql. 

Esses privilégios podem ser concedidos ao executar os seguintes comandos de concessão.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Para conceder permissões a um MySQL utilizador monitorização, o utilizador conceder tem de ter o privilégio "GRANT opção", bem como o privilégio de ser concedido.

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Depois de configurar o agente do Log Analytics para Linux enviar dados para o Azure Monitor, tem de configurar os contadores de desempenho a recolher.  Utilize o procedimento [Windows e Linux origens de dados de desempenho no Azure Monitor](data-sources-performance-counters.md) com os contadores na tabela seguinte.

| Nome do Objeto | Nome do Contador |
|:--|:--|
| Base de Dados MySQL | Espaço em disco, em Bytes |
| Base de Dados MySQL | Tabelas |
| Servidor MySQL | Ligação abortadas Pct |
| Servidor MySQL | Porcentagem de utilização da ligação |
| Servidor MySQL | Utilização de espaço em disco em Bytes |
| Servidor MySQL | Pct de análise de tabela completa |
| Servidor MySQL | Conjunto de memória intermédia de InnoDB pressionar Pct |
| Servidor MySQL | Pct de utilização de conjunto de memória intermédia de InnoDB |
| Servidor MySQL | Pct de utilização de conjunto de memória intermédia de InnoDB |
| Servidor MySQL | Pct de acerto na Cache de chave |
| Servidor MySQL | Porcentagem de utilização da Cache de chave |
| Servidor MySQL | Porcentagem de escrita da Cache de chave |
| Servidor MySQL | Porcentagem de acertos de Cache da consulta |
| Servidor MySQL | Porcentagem de Prunes da Cache de consulta |
| Servidor MySQL | Porcentagem de utilização da Cache de consulta |
| Servidor MySQL | Pct de acerto na Cache de tabela |
| Servidor MySQL | Porcentagem de utilização da Cache de tabela |
| Servidor MySQL | Pct de contenção de bloqueio de tabela |

## <a name="apache-http-server"></a>Apache HTTP Server 
Apache HTTP Server seja detetada no computador quando o pacote de omsagent é instalado, o fornecedor para Apache HTTP Server da monitorização de desempenho será instalado automaticamente. Este fornecedor se baseia num módulo de Apache que tem de ser carregado no Apache HTTP Server para aceder aos dados de desempenho. O módulo pode ser carregado com o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Descarregar o módulo de monitorização do Apache, execute o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Depois de configurar o agente do Log Analytics para Linux enviar dados para o Azure Monitor, tem de configurar os contadores de desempenho a recolher.  Utilize o procedimento [Windows e Linux origens de dados de desempenho no Azure Monitor](data-sources-performance-counters.md) com os contadores na tabela seguinte.

| Nome do Objeto | Nome do Contador |
|:--|:--|
| Apache HTTP Server | Operadores de ocupado |
| Apache HTTP Server | Funções de trabalho ociosas |
| Apache HTTP Server | Operadores de ocupado PCT |
| Apache HTTP Server | Pct total da CPU |
| Apache Virtual Host | Erros por minuto - cliente |
| Apache Virtual Host | Erros por minuto - servidor |
| Apache Virtual Host | KB por solicitação |
| Apache Virtual Host | Pedidos KB por segundo |
| Apache Virtual Host | Pedidos por segundo |



## <a name="next-steps"></a>Passos Seguintes
* [Recolher contadores de desempenho](data-sources-performance-counters.md) agentes de Linux.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 