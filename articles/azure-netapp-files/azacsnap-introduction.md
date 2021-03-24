---
title: O que é a ferramenta Azure Application Consistent Snapshot para ficheiros Azure NetApp | Microsoft Docs
description: Fornece uma introdução para a ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 4ba679459686340396e0e4d65344295c0fa9c4be
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869961"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>O que é a ferramenta Azure Application Consistent Snapshot (pré-visualização)

A ferramenta Azure Application Consistent Snapshot (AzAcSnap) é uma ferramenta de linha de comando que permite a proteção de dados para bases de dados de terceiros, manuseando toda a orquestração necessária para colocá-las em um estado consistente de aplicação antes de tirar uma foto de armazenamento, após a qual as devolve a um estado operacional.

## <a name="supported-platforms-and-os"></a>Plataformas apoiadas e OS

- **Bases de dados**
  - SAP HANA (consulte a [matriz de suporte](azacsnap-get-started.md#snapshot-support-matrix-from-sap) para mais detalhes)

- **Sistemas Operativos**
  - SUSE Linux Enterprise Server 12+
  - Red Hat Enterprise Linux 7+

## <a name="benefits-of-using-azacsnap"></a>Benefícios da utilização do AzAcSnap

O AzAcSnap aproveita as funcionalidades de instantâneo e replicação de volume em Ficheiros Azure NetApp e Azure Large Instance.  Proporciona os seguintes benefícios:

- **Proteção de dados consistente com aplicação** O AzAcSnap é uma solução centralizada para fazer o backup de ficheiros de bases de dados críticos. Garante a consistência da base de dados antes de efetuar uma imagem de volume de armazenamento. Como resultado, assegura que o instantâneo de volume de armazenamento pode ser usado para a recuperação da base de dados.
- **Gestão de catálogos de bases de dados** Quando utiliza o AzAcSnap com uma base de dados que tem um catálogo de backup incorporado, os registos dentro do catálogo são mantidos atualizados com instantâneos de armazenamento.  Esta capacidade permite que um administrador de base de dados veja a atividade de backup.
- **Proteção do volume ad hoc** Esta capacidade é útil para volumes não-base de base de dados que não precisam de quiescing de aplicação antes de tirar uma foto de armazenamento.  Exemplos incluem volumes de backup de log-backup SAP HANA ou volumes SAPTRANS.
- **Clonagem de volumes de armazenamento** Esta capacidade fornece clones de volume de armazenamento eficientes em termos espaciais para fins de desenvolvimento e teste.
- **Apoio à recuperação de desastres** O AzAcSnap aproveita a replicação do volume de armazenamento para fornecer opções para recuperar imagens replicadas consistentes com aplicações num site remoto.

AzAcSnap é um único binário.  Não necessita de agentes adicionais ou plug-ins para interagir com a base de dados ou o armazenamento (Ficheiros Azure NetApp via Azure Resource Manager, e Azure Large Instance via SSH).  O AzAcSnap deve ser instalado num sistema que tenha conectividade com a base de dados e o armazenamento.  No entanto, a flexibilidade da instalação e configuração permite uma única instalação centralizada ou uma instalação totalmente distribuída com cópias instaladas em cada instalação de base de dados.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

O AzAcSnap pode ser instalado no mesmo anfitrião que a base de dados (SAP HANA), ou pode ser instalado num sistema centralizado.  Mas, deve haver conectividade de rede para os servidores de base de dados e o back-end de armazenamento (Azure Resource Manager for Azure NetApp Files ou SSH for Azure Large Instance).

AzAcSnap é uma aplicação leve que é normalmente executada a partir de um programador externo.  Na maioria dos sistemas Linux, esta operação é `cron` , que é o que a documentação vai focar.  Mas o programador pode ser uma ferramenta alternativa desde que possa importar o `azacsnap` perfil de concha do utilizador.  Importar as definições ambientais do utilizador garante que os caminhos e permissões de ficheiros são inicializados corretamente.

## <a name="command-synopsis"></a>Sinopse de comando

O formato geral dos comandos é o seguinte: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Opções de comando

As opções de comando são as seguintes com os comandos como as principais balas e os sub-comandos associados como balas recortadas:

- **`-h`** fornece ajuda de linha de comando alargada com exemplos sobre o uso do AzAcSnap.
- **`-c configure`** Este comando fornece uma interface de estilo Q&A interativa para criar ou modificar o `azacsnap` ficheiro de configuração (padrão = `azacsnap.json` ).
  - **`--configuration new`** irá criar um novo ficheiro config.
  - **`--configuration edit`** irá editar um ficheiro config existente.
  - consulte a referência de [comando de configuração](azacsnap-cmd-ref-configure.md).
- **`-c test`** valida o ficheiro de configuração e a conectividade de teste.
  - **`--test hana`**  testa a ligação à instância SAP HANA.
  - **`--test storage`** testa a comunicação com a interface de armazenamento subjacente, criando um instantâneo de armazenamento temporário em todos os volumes configurados `data` e, em seguida, removendo-os.
  - **`--test all`** executará tanto os `hana` `storage` testes em sequência.
  - refere-se à referência de comando de [teste](azacsnap-cmd-ref-test.md).
- **`-c backup`** é o comando principal para executar instantâneos de armazenamento consistentes na base de dados para dados (volumes de dados SAP HANA) & outros volumes (por exemplo, partilhados, cópias de segurança de registo ou arranque).
  - **`--volume data`** para digitalizar todos os volumes na `dataVolume` estrofe do ficheiro de configuração.
  - **`--volume other`** para digitalizar todos os volumes na `otherVolume` estrofe do ficheiro de configuração.
  - consulte a [referência de comando de backup](azacsnap-cmd-ref-backup.md).
- **`-c details`** fornece informações sobre instantâneos ou replicação.
  - **`--details snapshots`** Fornece uma lista de detalhes básicos sobre as imagens para cada volume que foi configurado.
  - **`--details replication`** Fornece detalhes básicos em torno do estado de replicação do local de produção para o local de recuperação de desastres.
  - consulte a referência de [comando de detalhes](azacsnap-cmd-ref-details.md).
- **`-c delete`** Este comando elimina uma imagem de armazenamento ou um conjunto de instantâneos. Você pode usar o ID de backup SAP HANA como encontrado no HANA Studio ou o nome do instantâneo de armazenamento. O ID de backup está apenas ligado aos `hana` instantâneos, que são criados para os dados e volumes partilhados. Caso contrário, se o nome do instantâneo for introduzido, procura todas as imagens que correspondam ao nome do instantâneo introduzido.
  - ver a [eliminação](azacsnap-cmd-ref-delete.md).
- **`-c restore`** fornece dois métodos para restaurar um instantâneo para um volume, criando um novo volume com base no instantâneo ou rolando um volume para um estado de pré-visualização.
  - **`--restore snaptovol`** Cria um novo volume com base no último instantâneo no volume alvo.
  - **`-c restore --restore revertvolume`** Reverte o volume de alvo para um estado anterior com base no instantâneo mais recente.
  - consulte a referência de [comando de restauro](azacsnap-cmd-ref-restore.md).
- **`[--configfile <configfilename>]`** O parâmetro de linha de comando opcional para fornecer um nome de ficheiro de configuração JSON diferente.  Isto é particularmente útil para criar um ficheiro de configuração separado por SID (por `--configfile H80.json` exemplo).

## <a name="next-steps"></a>Passos seguintes

- [Começar com a ferramenta Azure Application Consistent Snapshot](azacsnap-get-started.md)
