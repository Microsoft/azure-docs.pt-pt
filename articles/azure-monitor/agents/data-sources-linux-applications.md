---
title: Colete o desempenho da aplicação Linux em Azure Monitor | Microsoft Docs
description: Este artigo fornece detalhes para configurar o agente Log Analytics para o Linux recolher contadores de desempenho para o MySQL e o Servidor HTTP Apache.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: d8bcc4b68082fdf0d1f86dc341c3313f9d8788c4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050686"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Recolha balcões de desempenho para aplicações Linux no Azure Monitor 

Este artigo fornece detalhes para configurar o agente Log Analytics para o [Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) recolher contadores de desempenho para aplicações específicas no Azure Monitor.  As candidaturas incluídas neste artigo são:  

- [MySQL](#mysql)
- [Servidor Apache HTTP](#apache-http-server)

## <a name="mysql"></a>MySQL
Se o MySQL Server ou o MariaDB Server forem detetados no computador quando o agente Log Analytics for instalado, será instalado automaticamente um fornecedor de monitorização de desempenho do MySQL Server. Este fornecedor conecta-se ao servidor Local MySQL/MariaDB para expor as estatísticas de desempenho. As credenciais de utilizador do MySQL devem ser configuradas para que o fornecedor possa aceder ao Servidor MySQL.

### <a name="configure-mysql-credentials"></a>Configure credenciais MySQL
O fornecedor MySQL OMI requer um utilizador MySQL pré-configurado e instalou bibliotecas de clientes MySQL para consultar as informações de desempenho e saúde a partir da instância MySQL.  Estas credenciais estão guardadas num ficheiro de autenticação que está armazenado no agente Linux.  O ficheiro de autenticação especifica qual o endereço de ligação e a porta em que a instância MySQL está a ouvir e quais as credenciais a utilizar para recolher métricas.  

Durante a instalação do agente Log Analytics para o Linux, o fornecedor MySQL OMI irá digitalizar os ficheiros de configuração MySQL my.cnf (localizações predefinitivas) para endereço de ligação e porta e definir parcialmente o ficheiro de autenticação OMI MySQL.

O ficheiro de autenticação MySQL é armazenado em `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth` .


### <a name="authentication-file-format"></a>Formato de ficheiro de autenticação
Segue-se o formato do ficheiro de autenticação MySQL OMI

> [Porta]=[Endereço de Ligação], [nome de utilizador], [Senha codificada base64]  
> (Porta)=(Endereço de ligação), (nome de utilizador), (Senha codificada base64)  
> (Porta)=(Endereço de ligação), (nome de utilizador), (Senha codificada base64)  
> AutoUpdate=[true|false]  

As entradas no ficheiro de autenticação são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Porta | Representa a porta atual onde o caso MySQL está a ouvir. A porta 0 especifica que as propriedades que se seguem são utilizadas por exemplo predefinido. |
| Bind-Address| Endereço de ligação MySQL atual. |
| nome de utilizador| O utilizador MySQL costumava usar para monitorizar a instância do servidor MySQL. |
| Senha codificada Base64| Palavra-passe do utilizador de monitorização MySQL codificado na Base64. |
| AutoUpdate| Especifica se deve rescanar para alterações no ficheiro my.cnf e substituir o ficheiro de autenticação OMI MySQL quando o Provedor OMI MySQL for atualizado. |

### <a name="default-instance"></a>Instância predefinida
O ficheiro de autenticação OMI MySQL pode definir uma instância padrão e um número de porta para facilitar a gestão de várias instâncias MySQL num hospedeiro Linux.  A instância por defeito é denotada por um caso com a porta 0. Todas as instâncias adicionais herdarão propriedades definidas a partir da instância padrão, a menos que especifiquem valores diferentes. Por exemplo, se o MySQL ouvir na porta '3308' for adicionado, o endereço de telefone, o nome de utilizador e a palavra-passe codificada Base64 serão utilizados para tentar monitorizar a audição do caso 3308. Se a instância no 3308 estiver ligada a outro endereço e utilizar o mesmo nome de utilizador e par de palavra-passe MySQL, apenas o endereço de ligação é necessário, e as outras propriedades serão herdadas.

O quadro seguinte tem definições de exemplo 

| Descrição | Ficheiro |
|:--|:--|
| Instância padrão e instância com a porta 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Instância padrão e instância com porta 3308 e nome de utilizador e senha diferentes. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI Authentication File Program
Incluído na instalação do provedor MySQL OMI é um programa de ficheiros de autenticação MySQL OMI que pode ser usado para editar o ficheiro de autenticação MySQL OMI. O programa de ficheiros de autenticação pode ser encontrado no local seguinte.

`/opt/microsoft/mysql-cimprov/bin/mycimprovauth`

> [!NOTE]
> O ficheiro de credenciais deve ser legível pela conta omsagent. É recomendável executar o comando mycimprovauth como omsgent.

A tabela seguinte fornece detalhes sobre a sintaxe para a utilização do mycimprovauth.

| Operação | Exemplo | Descrição
|:--|:--|:--|
| autoupdate *falso ou verdadeiro* | mycimprovauth autoupdate falso | Define se o ficheiro de autenticação será ou não atualizado automaticamente no reinício ou na atualização. |
| *senha de nome de nome de endereço de ligação* padrão | mycimprovauth padrão 127.0.0.1 pwd raiz | Define a instância padrão no ficheiro de autenticação MySQL OMI.<br>O campo de palavra-passe deve ser introduzido em texto simples - a palavra-passe no ficheiro de autenticação OMI MySQL será codificada na Base 64. |
| eliminar *o padrão ou port_num* | mycimprovauth 3308 | Elimina a instância especificada por defeito ou por número de porta. |
| Ajuda | ajuda mycimprov | Imprime uma lista de comandos a utilizar. |
| imprimir | impressão mycimprov | Imprime um ficheiro de autenticação OMI MySQL fácil de ler. |
| atualizar port_num *palavra-passe do nome de utilizador do endereço de ligação* | mycimprov atualização 3307 127.0.0.1 raiz pwd | Atualiza a instância especificada ou adiciona a instância se não existir. |

Os comandos de exemplo a seguir definem uma conta de utilizador predefinida para o servidor MySQL na localidade.  O campo de palavra-passe deve ser introduzido em texto simples - a palavra-passe no ficheiro de autenticação OMI MySQL será codificada na Base 64

```console
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
sudo /opt/omi/bin/service_control restart
```

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Permissões de base de dados necessárias para contadores de desempenho MySQL
O Utilizador MySQL requer acesso às seguintes consultas para recolher dados de desempenho do MySQL Server. 

```sql
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

O utilizador MySQL também requer acesso SELECT às seguintes tabelas predefinidos.

- information_schema
- mysql. 

Estes privilégios podem ser concedidos através da execução dos seguintes comandos de subvenção.

```sql
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

> [!NOTE]
> Para conceder permissões a um utilizador de monitorização MySQL, o utilizador que concede deve ter o privilégio de "opção GRANT", bem como o privilégio que está a ser concedido.

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Uma vez configurado o agente Log Analytics para o Linux enviar dados para o Azure Monitor, tem de configurar os contadores de desempenho para recolher.  Utilize o procedimento em [fontes de dados de desempenho do Windows e do Linux no Azure Monitor](data-sources-performance-counters.md) com os contadores na tabela seguinte.

| Nome do objeto | Nome do Contador |
|:--|:--|
| Base de Dados MySQL | Espaço em disco em Bytes |
| Base de Dados MySQL | Tabelas |
| Servidor MySQL | Pct de conexão abortada |
| Servidor MySQL | Uso de conexão Pct |
| Servidor MySQL | Uso do espaço do disco em bytes |
| Servidor MySQL | Tomografia completa da tabela Pct |
| Servidor MySQL | Piscina tampão Innodb hit Pct |
| Servidor MySQL | Piscina tampão Innodb Use Pct |
| Servidor MySQL | Piscina tampão Innodb Use Pct |
| Servidor MySQL | Cache Chave Hit Pct |
| Servidor MySQL | Cache Chave Usar Pct |
| Servidor MySQL | Cache Chave Escrever Pct |
| Servidor MySQL | Consulta Cache Hit Pct |
| Servidor MySQL | Consulta Cache Prunes Pct |
| Servidor MySQL | Consulta Cache Use Pct |
| Servidor MySQL | Cache de tabela Hit Pct |
| Servidor MySQL | Cache de tabela usar Pct |
| Servidor MySQL | Contenção de bloqueio de mesa Pct |

## <a name="apache-http-server"></a>Servidor Apache HTTP 
Se o Servidor HTTP Apache for detetado no computador quando o pacote omsagent for instalado, um fornecedor de monitorização de desempenho do Servidor HTTP Apache será automaticamente instalado. Este fornecedor baseia-se num módulo Apache que deve ser carregado no Servidor HTTP Apache para aceder aos dados de desempenho. O módulo pode ser carregado com o seguinte comando:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para descarregar o módulo de monitorização Apache, executar o seguinte comando:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definir contadores de desempenho

Uma vez configurado o agente Log Analytics para o Linux enviar dados para o Azure Monitor, tem de configurar os contadores de desempenho para recolher.  Utilize o procedimento em [fontes de dados de desempenho do Windows e do Linux no Azure Monitor](data-sources-performance-counters.md) com os contadores na tabela seguinte.

| Nome do objeto | Nome do Contador |
|:--|:--|
| Servidor Apache HTTP | Trabalhadores Ocupados |
| Servidor Apache HTTP | Trabalhadores Ociosos |
| Servidor Apache HTTP | Pct Trabalhadores Ocupados |
| Servidor Apache HTTP | Total Pct CPU |
| Hospedeiro Virtual Apache | Erros por Minuto - Cliente |
| Hospedeiro Virtual Apache | Erros por Minuto - Servidor |
| Hospedeiro Virtual Apache | KB por Pedido |
| Hospedeiro Virtual Apache | Pedidos KB por Segundo |
| Hospedeiro Virtual Apache | Pedidos por Segundo |



## <a name="next-steps"></a>Passos seguintes
* [Recolha contadores](data-sources-performance-counters.md) de desempenho de agentes Linux.
* Saiba mais [sobre consultas de registo](../logs/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.