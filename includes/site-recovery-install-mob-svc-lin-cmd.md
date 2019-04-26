---
ms.openlocfilehash: aa2e6d80620f0a4cf5063919a6de53e4de20f706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301448"
---
1. Copie o instalador para uma pasta local (por exemplo, /tmp) no servidor que pretende proteger. Num terminal, execute os seguintes comandos:
     ```
     cd /tmp;
     tar -xvzf Microsoft-ASR_UA*release.tar.gz
     ```
2. Para instalar o serviço de mobilidade, execute o seguinte comando:

     ```
     sudo ./install -d <Install Location> -r MS -v VmWare -q
     ```
3. Após a instalação estiver concluída, o serviço de mobilidade tem de estar registado para o servidor de configuração. Execute o seguinte comando para registar o serviço de mobilidade com o servidor de configuração:

     ```
     /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
     ```

#### <a name="mobility-service-installer-command-line"></a>Linha de comandos de instalador de serviço de mobilidade

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parâmetro|Type|Descrição|Valores possíveis|
|-|-|-|-|
|-r |Obrigatório|Especifica se deve ser instalado o serviço de mobilidade (MS) ou o destino mestre (TA) deve ser instalado.|MS <br /> MT|
|-d |Opcional|Localização onde o serviço de mobilidade está instalado.|/usr/local/ASR|
|-v|Obrigatório|Especifica a plataforma em que o serviço de mobilidade está instalado. <br /> <br />- **VMware**: Utilize este valor se instalar o serviço de mobilidade numa VM em execução no *anfitriões do VMware vSphere ESXi*, *anfitriões Hyper-V*, e *servidores físicos*. <br /> - **Azure**: Utilize este valor se instalar um agente numa VM de IaaS do Azure.| VMware <br /> Azure|
|-q|Opcional|Especifica a executar o instalador em modo silencioso.| N/A|

#### <a name="mobility-service-configuration-command-line"></a>Linha de comandos de configuração de serviço de mobilidade

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parâmetro|Type|Descrição|Valores possíveis|
|-|-|-|-|
|-i |Obrigatório|IP do servidor de configuração|Qualquer endereço IP válido|
|-P |Obrigatório|Caminho do ficheiro completo para o ficheiro em que a ligação passar a frase é guardado|Qualquer pasta válido|
<!--Update_Description: wording update-->
<!--ms.date: 03/05/2018-->