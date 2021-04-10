---
title: Conceitos de servidor - Base de dados Azure para MySQL Flexible Server
description: Este tópico fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para o MySQL Flexible Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a7978410dbe28a5da5dae81cb380d118fe13a159
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869383"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Conceitos de servidor em Azure Database para MySQL Flexible Server (Pré-visualização)

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para servidores flexíveis MySQL.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>O que é uma base de dados Azure para o MySQL Flexible Server?

Azure Database for MySQL Flexible Server é um serviço de base de dados totalmente gerido que executa a versão comunitária do MySQL. Em geral, o serviço foi concebido para fornecer flexibilidade e personalização de configuração com base nos requisitos do utilizador. É a mesma construção de servidor MySQL que você pode estar familiarizado no mundo do local. Especificamente, o servidor flexível é gerido, fornece fora do desempenho da caixa, melhor gestão e controlo do servidor, e expõe o acesso e funcionalidades ao nível do servidor.

Uma base de dados Azure para o MySQL Flexible Server:

- É criado dentro de uma subscrição Azure.
- É o recurso principal para bases de dados.
- Permite a configuração mySQL exposta através de parâmetros do Servidor (link para conceitos de parâmetros do Servidor).
- Executa cópias de segurança automatizadas e suporta ponto no tempo restaurado.
- Fornece um espaço de nome para bases de dados.
- É um recipiente com semântica vitalícia forte - elimine um servidor e elimine as bases de dados contidas.
- Colita recursos numa região.
- Suporte para o cliente fornecido agenda de manutenção do servidor
- Capacidade de implantar servidores flexíveis numa zona de configuração redundante para uma maior disponibilidade
- Fornece uma integração de rede virtual para o acesso ao servidor de base de dados
- Fornece forma de economizar custos fazendo uma pausa no servidor flexível quando não está a ser utilizado
- Fornece a margem de manobra para políticas de gestão que se aplicam às suas bases de dados: login, firewall, utilizadores, funções, configurações, etc.
- Suporta a versão principal MySQL 5.7 e MySQL 8.0. Para obter mais informações, consulte [a Base de Dados Azure suportada para versões do motor MySQL](./../concepts-supported-versions.md).

Dentro de uma Base de Dados Azure para o MySQL Flexible Server, pode criar uma ou várias bases de dados. Pode optar por criar uma única base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. O preço é estruturado por servidor, com base na configuração do nível de cálculo, vCores e armazenamento (GB). Para mais informações, consulte [o cálculo e o armazenamento.](./concepts-compute-storage.md)

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Parar/Iniciar uma base de dados Azure para o Servidor Flexível MySQL

Azure Database for MySQL Flexible Server dá-lhe a capacidade de **parar** o servidor quando não está a ser utilizado e **iniciar** o servidor quando retomar a atividade. Isto é essencialmente feito para economizar custos nos servidores da base de dados e apenas pagar pelo recurso quando está a ser utilizado. Isto torna-se ainda mais importante para cargas de trabalho de teste de dev e quando está a utilizar apenas o servidor durante uma parte do dia. Quando parar o servidor, todas as ligações ativas serão deixadas cair. Mais tarde, quando quiser voltar a colocar o servidor on-line, pode utilizar o [portal Azure](how-to-stop-start-server-portal.md) ou o CLI.

Quando o servidor está no estado **Stop,** o cálculo do servidor não é faturado. No entanto, o armazenamento continua a ser faturado à medida que o armazenamento do servidor permanece para garantir que os ficheiros de dados estão disponíveis quando o servidor é reiniciado.

> [!IMPORTANT]
> Quando **parares** o servidor, permanece nesse estado durante os próximos 7 dias de alongamento. Se não o **iniciar** manualmente durante este tempo, o servidor será automaticamente iniciado ao fim de 7 dias. Pode optar por **detê-lo** novamente se não estiver a utilizar o servidor.

Durante o período de paragem do servidor, não podem ser efetuadas operações de gestão no servidor. Para alterar quaisquer definições de configuração no servidor, terá de [iniciar o servidor](how-to-stop-start-server-portal.md). Consulte as [limitações de stop/start](./concepts-limitations.md#stopstart-operation).

## <a name="how-do-i-manage-a-server"></a>Como posso gerir um servidor?

Pode gerir a criação, eliminação, configuração de parâmetros do servidor (my.cnf), escala, rede, segurança, alta disponibilidade, backup & restaurar, monitorização da sua Base de Dados Azure para o MySQL Flexible Server utilizando o [portal Azure](./quickstart-create-server-portal.md) ou o [CLI Azure](./quickstart-create-server-cli.md). Além disso, os procedimentos armazenados estão disponíveis na Base de Dados Azure para o MySQL para executar certas tarefas de administração de base de dados necessárias, uma vez que o privilégio do utilizador SUPER não é suportado no servidor.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente ao [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) comando. Terminará a ligação associada à processlist_id fornecida após terminar qualquer declaração que a ligação esteja a executar.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente ao [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) comando. Terminará a declaração que a ligação está atualmente a executar. Deixa a ligação viva.|
|*mysql.az_load_timezone*|N/D|N/D|Cargas tabelas [de fuso horário](../howto-server-parameters.md#working-with-the-time-zone-parameter) para permitir que o `time_zone` parâmetro seja definido para valores nomeados (ex. "EUA/Pacífico").|


## <a name="next-steps"></a>Passos seguintes

-   Saiba mais [criar servidor](./quickstart-create-server-portal.md)
-   Saiba [mais sobre monitorização e alertas](./how-to-alert-on-metric.md)

