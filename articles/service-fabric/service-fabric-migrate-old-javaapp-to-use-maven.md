---
title: Migrar do Java SDK para Maven - Atualizar Aplicações Java do Azure Service Fabric antigas para utilizar o Maven | Microsoft Docs
description: Atualize as aplicações Java mais antigas que utilizam o Java SDK do Service Fabric, de modo a que obtenham as dependências Java do Service Fabric a partir do Maven. Depois de concluir esta configuração, as aplicações Java mais antigas poderão criar.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: rapatchi
ms.openlocfilehash: 75a3f8e6c872e0bbb9fde8c5776d99ba0cf2c3d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116605"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Atualizar a sua aplicação Java do Service Fabric anterior para obter as bibliotecas do Java a partir do Maven
Recentemente, movemos os binários Java do Service Fabric do Java SDK do Service Fabric para o alojamento Maven. Agora, pode utilizar **mavencentral** para obter as mais recentes dependências de Java do Service Fabric. Este início rápido ajuda-o a atualizar as suas aplicações Java existentes, que criou anteriormente para serem utilizadas com o Java SDK do Service Fabric, através de um modelo do Yeoman ou do Eclipse, para compatibilidade com a compilação baseada no Maven.

## <a name="prerequisites"></a>Pré-requisitos
1. Primeiro, tem de desinstalar o Java SDK existente.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```
2. Siga os passos mencionados [aqui](service-fabric-cli.md) para instalar a última CLI do Service Fabric.

3. Para criar e trabalhar nas aplicações Java do Service Fabric, tem de garantir que tem instalado o JDK 1.8 e o Gradle. Se ainda não estiverem instalados, execute o seguinte para instalar o JDK 1.8 (openjdk-8-jdk) e Gradle

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```
4. Atualize os scripts de instalação/desinstalação da sua aplicação, para que utilizem a nova CLI do Service Fabric, seguindo os passos mencionados [aqui](service-fabric-application-lifecycle-sfctl.md). Pode ver os nossos [exemplos](https://github.com/Azure-Samples/service-fabric-java-getting-started) de introdução como referência.

>[!TIP]
> O Yeoman não funcionará depois de desinstalar o Java SDK do Service Fabric. Siga os pré-requisitos mencionados [aqui](service-fabric-create-your-first-linux-application-with-java.md) para pôr em funcionamento o gerador de modelos Java Yeoman do Service Fabric.

## <a name="service-fabric-java-libraries-on-maven"></a>Bibliotecas Java do Service Fabric no Maven
As bibliotecas Java do Service Fabric foram alojadas no Maven. Pode adicionar as dependências no ``pom.xml`` ou ``build.gradle`` dos seus projetos para utilizar bibliotecas Java do Service Fabric a partir de **mavenCentral**.

### <a name="actors"></a>Atores

Suporte de Reliable Actor do Service Fabric para a sua aplicação.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Serviços

Suporte de Serviço sem Estado do Service Fabric para a sua aplicação.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Outros
#### <a name="transport"></a>Transporte

Suporte da camada de transporte para a aplicação Java do Service Fabric. Não é necessário adicionar esta dependência explicitamente às suas aplicações Reliable Actor ou Serviço, a não ser que o programe na camada de transporte.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Suporte para o Fabric

Suporte ao nível do sistema para o Service Fabric, que comunica com o runtime do Service Fabric nativo. Não é necessário adicionar esta dependência explicitamente às suas aplicações Reliable Actor ou Serviço. Isto é obtido automaticamente do Maven, quando incluir as outras dependências acima.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```


## <a name="migrating-service-fabric-stateless-service"></a>Migrar Serviço sem Estado do Service Fabric

Para poder criar o seu serviço Java sem estado do Service Fabric já existente mediante a utilização das dependências do Service Fabric obtidas a partir do Maven, tem de atualizar o ficheiro ``build.gradle`` dentro do Serviço. Anteriormente, tinha o aspeto seguinte -
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```
Agora, para obter as dependências do Maven, as partes correspondentes do ``build.gradle`` **atualizado** teriam o aspeto -
```
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```
De um modo geral, para ter uma ideia genérica do aspeto do script de compilação para um serviço Java sem Estado do Service Fabric, pode ver qualquer um dos exemplos da introdução. Eis o [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle) do exemplo EchoServer.

## <a name="migrating-service-fabric-actor-service"></a>Migrar o Serviço Actor do Service Fabric

Para poder criar a sua aplicação Java Actor do Service Fabric já existente mediante a utilização das dependências do Service Fabric obtidas a partir do Maven, tem de atualizar o ficheiro ``build.gradle`` dentro do pacote da interface e do pacote do Serviço. Se tiver um pacote TestClient, também tem de o atualizar. Assim, para o ator ``Myactor``, terá de atualizar os locais seguintes -
```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Atualizar o script de compilação para o projeto de interface

Anteriormente, tinha o aspeto seguinte -
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```
Agora, para obter as dependências do Maven, as partes correspondentes do ``build.gradle`` **atualizado** teriam o aspeto -
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Atualizar o script de compilação para o projeto de ator

Anteriormente, tinha o aspeto seguinte -
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```
Agora, para obter as dependências do Maven, as partes correspondentes do ``build.gradle`` **atualizado** teriam o aspeto -
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Atualizar o script de compilação para o projeto de cliente de teste

As alterações nesta secção são semelhantes às da secção anterior, ou seja, do projeto de ator. Anteriormente, o script do Gradle tinha o aspeto seguinte:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
      compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
  destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```
Agora, para obter as dependências do Maven, as partes correspondentes do ``build.gradle`` **atualizado** teriam o aspeto -
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Passos Seguintes

* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Interagir com os clusters do Service Fabric através da CLI do Service Fabric](service-fabric-cli.md)
