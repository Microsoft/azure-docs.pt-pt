---
title: Recolher desempenho da aplicação Linux no Azure Monitor Microsoft Docs
description: Este artigo fornece detalhes para configurar o agente Log Analytics para o Linux recolher contadores de desempenho para MySQL e Apache HTTP Server.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: 2fd148dbb85a4fd60fe63d4fb73128bf92dea1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670564"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Colete contadores de desempenho para aplicações Linux no Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Este artigo fornece detalhes para configurar o agente Log Analytics para o [Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) recolher contadores de desempenho para aplicações específicas no Monitor Azure.  As aplicações incluídas neste artigo são:  

- [MySQL](#mysql)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Se o MySQL Server ou o Servidor MariaDB forem detetados no computador quando o agente Log Analytics estiver instalado, será instalado automaticamente um fornecedor de monitorização de desempenho para o MySQL Server. Este fornecedor conecta-se ao servidor local MySQL/MariaDB para expor estatísticas de desempenho. As credenciais de utilizador mySQL devem ser configuradas para que o fornecedor possa aceder ao Servidor MySQL.

### <a name="configure-mysql-credentials"></a>Configure as credenciais MySQL
O fornecedor mySQL OMI requer um utilizador MySQL reconfigurado e instalou bibliotecas de clientes MySQL para consultar o desempenho e informações de saúde da instância MySQL.  Estas credenciais estão armazenadas num ficheiro de autenticação que está guardado no agente Linux.  O ficheiro de autenticação especifica o endereço de ligação e a porta que a instância MySQL está a ouvir e quais as credenciais a utilizar para recolher métricas.  

Durante a instalação do agente Log Analytics para o Linux, o fornecedor MySQL OMI irá digitalizar os ficheiros de configuração MySQL my.cnf (localizações predefinidas) para endereço de ligação e porta e definir parcialmente o ficheiro de autenticação MySQL OMI.

O ficheiro de autenticação MySQL está armazenado em `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formato de ficheiro de autenticação
Segue-se o formato para o ficheiro de autenticação MySQL OMI

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

As entradas no ficheiro de autenticação são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Porta | Representa a porta atual que a instância MySQL está a ouvir. A porta 0 especifica que as propriedades que se seguem são utilizadas por exemplo. |
| Endereço de ligação| Endereço de ligação MySQL atual. |
| o nome de utilizador| O utilizador MySQL usado para monitorizar a instância do servidor MySQL. |
| Palavra-passe codificada base64| Palavra-passe do utilizador de monitorização MySQL codificado na Base64. |
| Atualização automática| Especifica se deve rescanar para alterações no ficheiro my.cnf e substituir o ficheiro de autenticação MySQL OMI quando o Fornecedor OMI MySQL for atualizado. |

### <a name="default-instance"></a>Instância predefinida
O ficheiro de autenticação MySQL OMI pode definir uma instância padrão e um número de porta para facilitar a gestão de várias instâncias MySQL num hospedeiro Linux.  A instância predefinida é denotada por um caso com a porta 0. Todas as instâncias adicionais herdarão propriedades definidas a partir da instância padrão, a menos que especifiquem valores diferentes. Por exemplo, se for adicionada a audição da mesql na porta '3308', o endereço de ligação, o nome de utilizador e a senha codificada base64 serão utilizados para tentar monitorizar a audição da instância no 3308. Se a instância no 3308 estiver ligada a outro endereço e utilizar o mesmo nome de utilizador MySQL e par de palavras-passe apenas o endereço de ligação é necessário, e as outras propriedades serão herdadas.

A tabela seguinte tem definições de exemplo 

| Descrição | Ficheiro |
|:--|:--|
| Exemplo padrão e instância com porta 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Exemplo padrão e instância com a porta 3308 e diferente nome de utilizador e palavra-passe. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Programa de Ficheiros de Autenticação OMI mySQL
Incluído na instalação do fornecedor MySQL OMI está um programa de ficheiros de autenticação MySQL OMI que pode ser usado para editar o ficheiro de autenticação OMI MySQL. O programa de ficheiros de autenticação pode ser encontrado no seguinte local.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> O ficheiro de credenciais deve ser legível pela conta omsagent. É recomendável o comando mycimprovauth como omsgent.

A tabela seguinte fornece detalhes sobre a sintaxe para a utilização de mycimprovauth.

| Operação | Exemplo | Descrição
|:--|:--|:--|
| auto-actualização *falso ou verdadeiro* | mycimprovauth autoupdate falso | Define se o ficheiro de autenticação será ou não atualizado automaticamente no reinício ou atualização. |
| senha de nome de utilizador de *endereço de ligação* padrão | mycimprovauth padrão 127.0.0.1 raiz pwd | Define a instância predefinida no ficheiro de autenticação MySQL OMI.<br>O campo de palavra-passe deve ser introduzido em texto simples - a palavra-passe no ficheiro de autenticação MySQL OMI será codificada pela Base 64. |
| eliminar *o padrão ou port_num* | mycimprovauth 3308 | Elimina a instância especificada por padrão ou por número de porta. |
| Ajuda | mycimprov ajuda | Imprime uma lista de comandos a utilizar. |
| imprimir | impressão mycimprov | Imprime um ficheiro de autenticação OMI MySQL fácil de ler. |
| atualizar port_num *palavra-passe de nome de utilizador de endereço de ligação* | mycimprov update 3307 127.0.0.1 root pwd | Atualiza a instância especificada ou adiciona a instância se não existir. |

Os comandos de exemplo seguintes definem uma conta de utilizador padrão para o servidor MySQL no local de localização.  O campo de palavra-passe deve ser introduzido em texto simples - a palavra-passe no ficheiro de autenticação MySQL OMI será codificada pela Base 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Permissões de base de dados necessárias para contadores de desempenho MySQL
O Utilizador MySQL requer acesso às seguintes consultas para recolher os dados de desempenho do MySQL Server. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


O utilizador MySQL também requer acesso SELECT às seguintes tabelas predefinidas.

- information_schema
- mysql. 

Estes privilégios podem ser concedidos executando os seguintes comandos de subvenção.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Para conceder permissões a um utilizador de monitorização MySQL, o utilizador que concede deve ter o privilégio de "opção GRANT", bem como o privilégio concedido.

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Assim que configurar o agente Log Analytics para o Linux enviar dados para o Monitor Azure, tem de configurar os contadores de desempenho para recolher.  Utilize o procedimento em fontes de dados de [desempenho do Windows e do Linux no Monitor De](data-sources-performance-counters.md) desempenho do Azure com os contadores na tabela seguinte.

| Nome do objeto | Contra nome |
|:--|:--|
| Base de Dados MySQL | Espaço disco em Bytes |
| Base de Dados MySQL | Tabelas |
| Servidor MySQL | Conexão Abortada Pct |
| Servidor MySQL | Uso de ligação Pct |
| Servidor MySQL | Uso do espaço do disco em bytes |
| Servidor MySQL | Varredura de mesa completa Pct |
| Servidor MySQL | Piscina tampão innoDB hit pct |
| Servidor MySQL | Piscina tampão innoDB Use Pct |
| Servidor MySQL | Piscina tampão innoDB Use Pct |
| Servidor MySQL | Cache chave Hit Pct |
| Servidor MySQL | Utilização de cache chave Pct |
| Servidor MySQL | Cache chave Escrever Pct |
| Servidor MySQL | Cache De Consulta Hit Pct |
| Servidor MySQL | Perguntas Cache Ameixas Pct |
| Servidor MySQL | Consulta Cache Use Pct |
| Servidor MySQL | Cache de mesa hit Pct |
| Servidor MySQL | Uso de cache de mesa pct |
| Servidor MySQL | Contenção de fechadura de mesa pct |

## <a name="apache-http-server"></a>Apache HTTP Server 
Se o Apache HTTP Server for detetado no computador quando o pacote omsagent estiver instalado, será instalado automaticamente um fornecedor de monitorização de desempenho do Apache HTTP Server. Este fornecedor conta com um módulo Apache que deve ser carregado no Apache HTTP Server para aceder aos dados de desempenho. O módulo pode ser carregado com o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para descarregar o módulo de monitorização Apache, execute o seguinte comando:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Assim que configurar o agente Log Analytics para o Linux enviar dados para o Monitor Azure, tem de configurar os contadores de desempenho para recolher.  Utilize o procedimento em fontes de dados de [desempenho do Windows e do Linux no Monitor De](data-sources-performance-counters.md) desempenho do Azure com os contadores na tabela seguinte.

| Nome do objeto | Contra nome |
|:--|:--|
| Apache HTTP Server | Trabalhadores Ocupados |
| Apache HTTP Server | Trabalhadores ociosos |
| Apache HTTP Server | Pct Trabalhadores Ocupados |
| Apache HTTP Server | Total Pct CPU |
| Anfitrião Virtual Apache | Erros por Minuto - Cliente |
| Anfitrião Virtual Apache | Erros por Minuto - Servidor |
| Anfitrião Virtual Apache | KB por Pedido |
| Anfitrião Virtual Apache | Pedidos KB por Segundo |
| Anfitrião Virtual Apache | Pedidos por Segundo |



## <a name="next-steps"></a>Passos seguintes
* [Recolha contadores](data-sources-performance-counters.md) de desempenho de agentes linux.
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados. 
