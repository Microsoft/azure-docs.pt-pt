---
title: Compreender a nuvem
description: Mergulho profundo para entender o fornecimento de um VM Azure usando cloud-init.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: f5028abadbe5600058c83a144d0095aee1278fe6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86042084"
---
# <a name="diving-deeper-into-cloud-init"></a>Mergulhar mais profundamente na nuvem
Para saber mais sobre [o cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) ou resolver problemas a um nível mais profundo, você precisa entender como funciona. Este documento destaca as partes importantes e explica as especificidades do Azure.

Quando a nuvem é incluída numa imagem generalizada, e um VM é criado a partir dessa imagem, irá processar configurações e passar por 5 fases durante o arranque inicial. Estas fases importam, como mostra em que ponto a nuvem-init vai aplicar configurações. 


## <a name="understand-cloud-init-configuration"></a>Compreender a configuração Cloud-Init
Configurar um VM para funcionar numa plataforma, significa que a cloud-init precisa de aplicar várias configurações, como consumidor de imagem, as principais configurações com que irá interagir é `User data` (customData), que suporta vários formatos, estas são documentadas [aqui.](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats) Também tem a capacidade de adicionar e executar scripts (/var/lib/cloud/scripts) para configuração adicional, abaixo discute-o com mais detalhes.

Algumas configurações já estão cozidas em imagens do Azure Marketplace que vêm com cloud-init, tais como:

1. Fonte de **dados** em nuvem - o cloud-init contém código que pode interagir com plataformas de nuvem, estas são chamadas 'datasources'. Quando um VM é criado a partir de uma imagem em nuvem em [Azure,](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)o cloud-init carrega a fonte de dados Azure, que irá interagir com os pontos finais de metadados Azure para obter a configuração específica do VM.
2. **Config de tempo** de execução (/run/cloud-init)
3. **Config de imagem** (/etc/nuvem), `/etc/cloud/cloud.cfg` `/etc/cloud/cloud.cfg.d/*.cfg` como, . Um exemplo de onde isto é usado em Azure, é comum que as imagens Linux OS com nuvem-init tenham uma diretiva de fonte de dados Azure, que diz em nuvem-init que fonte de dados deve usar, isto poupa tempo de cloud-init:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Fases de arranque de init em nuvem (configuração de processamento)

Ao a provisionar com cloud-init, existem 5 fases de arranque, que configuram o processo, e mostradas nos registos.

1. [Fase do Gerador](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): O gerador sistema de cloud-in começa e determina que o init de nuvem deve ser incluído nos objetivos de arranque e, em caso afirmativo, permite a intenção de nuvens. 

2. [Cloud-init Local Stage](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): Aqui a cloud-init procurará o dado local "Azure", que permitirá a interface em nuvem com o Azure, e aplicará uma configuração de networking, incluindo o retorno.

3. [Fase init init cloud -init (Rede)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): A rede deve estar on-line e a informação do NIC e do quadro de rotas deve ser gerada. Nesta fase, serão executados os módulos listados `cloud_init_modules` em /etc/cloud/cloud.cfg. O VM em Azure será montado, o disco efémero é formatado, o nome de hospedeiro é definido, juntamente com outras tarefas.

   Estes são alguns `cloud_init_modules` dos:
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Após esta fase, o cloud-in irá sinalizar para a plataforma Azure que o VM foi a provisionado com sucesso. Alguns módulos podem ter falhado, nem todas as falhas do módulo resultarão numa falha de provisionamento.

4. [Fase Config em nuvem](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): Nesta fase, serão executados os módulos `cloud_config_modules` em /etc/cloud/cloud.cfg.


5. [Fase Final cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): Nesta fase final, serão executados os módulos em `cloud_final_modules` , listados em /etc/cloud/cloud.cfg. Aqui os módulos que precisam de ser executados tardiamente no processo de arranque, como instalações de pacotes e scripts de execução etc. 

   -   Durante esta fase, você pode executar scripts colocando-os nos diretórios em `/var/lib/cloud/scripts` :
   - `per-boot` - scripts dentro deste diretório, executados em cada reboot
   - `per-instance` - scripts dentro deste diretório executado quando uma nova instância é inicialmente iniciado
   - `per-once` - os scripts dentro deste diretório só funcionam uma vez

## <a name="next-steps"></a>Passos seguintes

[Resolução de problemas na nuvem.](cloud-init-troubleshooting.md)
