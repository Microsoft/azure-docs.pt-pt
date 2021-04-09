---
title: Começa com a ferramenta Azure Application Consistent Snapshot para ficheiros Azure NetApp | Microsoft Docs
description: Fornece um guia para instalar a ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736367"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Começa com a ferramenta Azure Application Consistent Snapshot (pré-visualização)

Este artigo fornece um guia para a instalação da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

## <a name="getting-the-snapshot-tools"></a>Obtenção das ferramentas instantâneas

Recomenda-se que os clientes obtenham a versão mais recente do [AzAcSnap Installer](https://aka.ms/azacsnapdownload) da Microsoft.

O ficheiro de auto-instalação tem um ficheiro de [assinatura AzAcSnap Installer](https://aka.ms/azacsnapdownloadsignature) associado que é assinado com a chave pública da Microsoft para permitir a verificação de GPG do instalador descarregado.

Assim que estes downloads estiverem concluídos, siga os passos deste guia para instalar.

### <a name="verifying-the-download"></a>Verificação do download

O instalador, que é transferível por acima, tem um ficheiro de assinatura PGP associado com uma `.asc` extensão de nome de ficheiro. Este ficheiro pode ser utilizado para garantir que o instalador descarregado é um ficheiro fornecido pela Microsoft. A Chave Pública MICROSOFT PGP utilizada para a assinatura de pacotes Linux está disponível aqui ( <https://packages.microsoft.com/keys/microsoft.asc> ) e foi usada para assinar o ficheiro de assinatura.

A Chave Pública PGP da Microsoft pode ser importada para o local de um utilizador da seguinte forma:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Os seguintes comandos confiam na Chave Pública PGP da Microsoft:

1. Lista as chaves da loja.
2. Edite a chave Microsoft.
3. Verifique a impressão digital com `fpr`
4. Assine a chave para confiar.

```bash
gpg --list-keys
```

Chaves listadas:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Saída da sessão interativa `gpg` que assina a chave pública da Microsoft:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

O ficheiro de assinatura PGP para o instalador pode ser verificado da seguinte forma:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Para obter mais detalhes sobre a utilização do GPG, consulte [o Manual de Privacidade da GNU](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Cenários suportados

As ferramentas instantâneas podem ser utilizadas nos seguintes cenários.

- SID único
- Vários SID
- HSR
- Aumentar
- MDC (Apenas inquilino único apoiado)
- Contentor Único
- Sistema Operativo SUSE
- Sistema operativo RHEL
- SKU TIPO I
- SKU TIPO II

Ver [cenários apoiados para grandes instâncias HANA](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>Matriz de suporte instantâneo da SAP

A matriz a seguir é fornecida como uma orientação sobre as versões de SAP HANA são suportadas pela SAP para armazenamento de backups instantâneos.

| Versões de base de dados       |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Base de Dados de Contentores Únicos| √       | √      | -      | -      | -      | -      |
|MDC Inquilino Único        | -       | -      | √      | √      | √      | √      |
|MDC MDC MDC Múltiplos Inquilinos     | -       | -      | -      | -      | -      | √      |
> √ = <small>suportado pela SAP para instantâneos de armazenamento</small>

## <a name="important-things-to-remember"></a>Coisas importantes para lembrar

- Após a configuração das ferramentas instantâneas, monitorize continuamente o espaço de armazenamento disponível e, se necessário, elimine regularmente as imagens antigas para evitar o preenchimento do armazenamento.
- Utilize sempre as últimas ferramentas instantâneas.
- Use a mesma versão das ferramentas instantâneas em toda a paisagem.
- Teste as ferramentas instantâneas e fique confortável com os parâmetros necessários e saída do comando antes de utilizar no sistema de produção.
- Ao configurar o utilizador HANA para cópia de segurança (detalhes abaixo neste documento), é necessário configurar o utilizador para cada instância HANA. Crie uma conta de utilizador SAP HANA para aceder à instância HANA sob o SYSTEMDB (e não na base de dados SID) para MDC. No ambiente de contentores únicos, pode ser criado sob a base de dados dos inquilinos.
- Os clientes devem fornecer a chave pública SSH para o acesso ao armazenamento. Esta ação deve ser feita uma vez por nó e para cada utilizador sob o qual o comando é executado.
- O número de instantâneos por volume é limitado a 250.
- Se editar manualmente o ficheiro de configuração, utilize sempre um editor de texto Linux como "vi" e não editores do Windows como o Notepad. A utilização do editor do Windows pode corromper o formato de ficheiro.
  - Configurar `hdbuserstore` para o utilizador SAP HANA comunicar com o SAP HANA.
- Para DR: As ferramentas instantâneas devem ser testadas no nó DR antes da instalação do DR.
- Monitor de disquet regularmente, a eliminação automatizada de registos é gerida com a `--trim` opção do   `azacsnap -c backup` SAP HANA 2 e posteriores lançamentos.
- **Risco de não serem tomadas imagens** - As ferramentas instantâneas interagem apenas com o nó do sistema SAP HANA especificado no ficheiro de configuração.  Se este nó ficar indisponível, não existe nenhum mecanismo para começar automaticamente a comunicar com outro nó.  
  - Para um **SAP HANA Scale-Out com** cenário de Standby é típico instalar e configurar as ferramentas instantâneas no nó principal. Mas, se o nó mestre ficar indisponível, o nó de espera assumirá o papel de nó principal. Neste caso, a equipa de implementação deve configurar as ferramentas instantâneas em ambos os nós (Master e Stand-By) para evitar quaisquer instantâneos perdidos. No estado normal, o nó mestre tomará instantâneos HANA iniciados por crontab, mas depois do nó mestre falhar por esses instantâneos terá que ser executado a partir de outro nó, como o novo nó mestre (ex-standby). Para alcançar este resultado, o nó de espera necessitaria da ferramenta instantânea instalada, da comunicação de armazenamento ativada, da hdbuserstore configurada, `azacsnap.json` configurada e dos comandos crontab encenados antes da falha.
  - Para um cenário **SAP HANA HSR HA,** recomenda-se instalar, configurar e agendar as ferramentas instantâneas nos nós (Primário e Secundário). Em seguida, se o nó primário ficar indisponível, o nó secundário assumirá com as fotos a serem tiradas no Secundário. No estado normal, o nó primário tomará instantâneos HANA iniciados por crontab e o nó secundário tentaria tirar fotos, mas falhará como o Primário está funcionando corretamente.  Mas depois do nó primário falhar, essas fotos serão executadas a partir do nó secundário. Para alcançar este resultado, o nó secundário necessita da ferramenta instantânea instalada, da comunicação de armazenamento `hdbuserstore` ativada, configurada, azacsnap.jsem configurado e crontab ativada antes da falha.

## <a name="guidance-provided-in-this-document"></a>Orientação fornecida neste documento

São fornecidas as seguintes orientações para ilustrar a utilização das ferramentas instantâneas.

### <a name="taking-snapshot-backups"></a>Tomando backups instantâneos

- [Quais são os pré-requisitos para o instantâneo de armazenamento](azacsnap-installation.md#prerequisites-for-installation)
  - [Permitir a comunicação com o armazenamento](azacsnap-installation.md#enable-communication-with-storage)
  - [Ativar a comunicação com a SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Como tirar fotos manualmente](azacsnap-tips.md#take-snapshots-manually)
- [Como configurar a cópia de segurança automática do instantâneo](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Como monitorizar as imagens](azacsnap-tips.md#monitor-the-snapshots)
- [Como apagar uma foto?](azacsnap-tips.md#delete-a-snapshot)
- [Como restaurar um instantâneo](azacsnap-tips.md#restore-a-snapshot)
- [Como restaurar um `boot` instantâneo](azacsnap-tips.md#restore-a-boot-snapshot)
- [Quais são os principais factos a saber sobre os instantâneos](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> As imagens são testadas tanto para SID simples como para vários SID.

### <a name="performing-disaster-recovery"></a>Realização de recuperação de desastres

- [Quais são os pré-requisitos para a configuração de DR](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Como preparar uma recuperação de desastres](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Como monitorizar a replicação de dados do site Primário para DR](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Como executar um failover para o site DR?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Passos seguintes

- [Instalar ferramenta instantânea consistente da aplicação Azure](azacsnap-installation.md)