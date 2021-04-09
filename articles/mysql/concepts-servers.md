---
title: Conceitos de servidor - Azure Database for MySQL
description: Este tópico fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para servidores MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cb8394de49c2c5daeae156a9316466928eded148
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628480"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Conceitos de servidor na Base de Dados Azure para o MySQL

Este artigo fornece considerações e diretrizes para trabalhar com a Base de Dados Azure para servidores MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>O que é uma Base de Dados Azure para o servidor MySQL?

Uma Base de Dados Azure para o servidor MySQL é um ponto administrativo central para várias bases de dados. É a mesma construção de servidor MySQL que você pode estar familiarizado no mundo do local. Especificamente, a Base de Dados Azure para o serviço MySQL é gerida, fornece garantias de desempenho e expõe o acesso e funcionalidades ao nível do servidor.

Uma base de dados Azure para o servidor MySQL:

- É criado dentro de uma subscrição Azure.
- É o recurso principal para bases de dados.
- Fornece um espaço de nome para bases de dados.
- É um recipiente com semântica vitalícia forte - elimine um servidor e elimine as bases de dados contidas.
- Colita recursos numa região.
- Fornece um ponto final de ligação para o acesso ao servidor e à base de dados.
- Fornece a margem de manobra para políticas de gestão que se aplicam às suas bases de dados: login, firewall, utilizadores, funções, configurações, etc.
- Está disponível em várias versões. Para obter mais informações, consulte [a Base de Dados Azure suportada para versões de base de dados MySQL](./concepts-supported-versions.md).

Dentro de uma Base de Dados do Azure para o servidor MySQL, pode criar uma ou várias bases de dados. Pode optar por criar uma única base de dados por servidor para utilizar todos os recursos ou criar várias bases de dados para partilhar os recursos. O preço é estruturado por servidor, com base na configuração do nível de preços, vCores e armazenamento (GB). Para obter mais informações, consulte [os níveis de preços.](./concepts-pricing-tiers.md)

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Como posso ligar e autenticar a uma Base de Dados Azure para o servidor MySQL?

Os seguintes elementos ajudam a garantir um acesso seguro à sua base de dados.

| Conceito de segurança | Description     |
| :-- | :-- |
| **Autenticação e autorização** | A base de dados Azure para servidor MySQL suporta a autenticação nativa do MySQL. Pode ligar e autenticar num servidor com o login de administração do servidor. |
| **Protocolo** | O serviço suporta um protocolo baseado em mensagens utilizado pelo MySQL. |
| **TCP/IP** | O protocolo é suportado sobre TCP/IP e sobre tomadas de domínio Unix. |
| **Firewall** | Para ajudar a proteger os seus dados, uma regra de firewall impede todo o acesso ao servidor de base de dados, até especificar quais os computadores que têm permissão. Consulte [a base de dados Azure para as regras de firewall do MySQL Server](./concepts-firewall-rules.md). |
| **SSL** | O serviço suporta a aplicação de ligações SSL entre as suas aplicações e o servidor de base de dados.  Veja [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para o MySQL). |

## <a name="stopstart-an-azure-database-for-mysql"></a>Parar/Iniciar uma Base de Dados Azure para o MySQL

A Azure Database for MySQL dá-lhe a capacidade de **parar** o servidor quando não está a ser utilizado e **iniciar** o servidor quando retomar a atividade. Isto é essencialmente feito para economizar custos nos servidores da base de dados e apenas pagar pelo recurso quando está a ser utilizado. Isto torna-se ainda mais importante para cargas de trabalho de teste de dev e quando está a utilizar apenas o servidor durante uma parte do dia. Quando parar o servidor, todas as ligações ativas serão deixadas cair. Mais tarde, quando quiser voltar a colocar o servidor on-line, pode utilizar o [portal Azure](how-to-stop-start-server.md) ou [o CLI](how-to-stop-start-server.md).

Quando o servidor está no estado **Stop,** o cálculo do servidor não é faturado. No entanto, o armazenamento continua a ser faturado à medida que o armazenamento do servidor permanece para garantir que os ficheiros de dados estão disponíveis quando o servidor é reiniciado.

> [!IMPORTANT]
> Quando **parares** o servidor, permanece nesse estado durante os próximos 7 dias de alongamento. Se não o **iniciar** manualmente durante este tempo, o servidor será automaticamente iniciado ao fim de 7 dias. Pode optar por **detê-lo** novamente se não estiver a utilizar o servidor.

Durante o período de paragem do servidor, não podem ser efetuadas operações de gestão no servidor. Para alterar quaisquer definições de configuração no servidor, terá de [iniciar o servidor](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Limitações da operação stop/start
- Não suportado com configurações de réplicas de leitura (fonte e réplicas).

## <a name="how-do-i-manage-a-server"></a>Como posso gerir um servidor?

Pode gerir a criação, eliminação, configuração de parâmetros do servidor (my.cnf), escala, rede, segurança, alta disponibilidade, backup & restaurar, monitorização da sua Base de Dados Azure para servidores MySQL utilizando o portal Azure ou o CLI Azure. Além disso, os procedimentos armazenados estão disponíveis na Base de Dados Azure para o MySQL para executar certas tarefas de administração de base de dados necessárias, uma vez que o privilégio do utilizador SUPER não é suportado no servidor.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente ao [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) comando. Terminará a ligação associada à processlist_id fornecida após terminar qualquer declaração que a ligação esteja a executar.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente ao [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) comando. Terminará a declaração que a ligação está atualmente a executar. Deixa a ligação viva.|
|*mysql.az_load_timezone*|N/D|N/D|Cargas tabelas [de fuso horário](howto-server-parameters.md#working-with-the-time-zone-parameter) para permitir que o `time_zone` parâmetro seja definido para valores nomeados (ex. "EUA/Pacífico").|

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do serviço, consulte [a Base de Dados Azure para a visão geral do MySQL](./overview.md)
- Para obter informações sobre quotas e limitações específicas de recursos com base no seu **nível de preços,** consulte [os níveis de preços](./concepts-pricing-tiers.md)
- Para obter informações sobre a ligação ao serviço, consulte [as bibliotecas connection para Azure Database for MySQL](./concepts-connection-libraries.md).
