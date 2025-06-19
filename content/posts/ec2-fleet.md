+++
date = '2025-03-09T20:14:52-03:00'
draft = false
title = 'Ec2 Fleet - Como Criar uma Frota de EC2 e fazer o escalonamento'
description = 'Como Criar uma Frota de EC2 com Load Balancer, Snapshots e Auto Scaling na AWS'
tags = ['EC2', 'blog', 'hugo']
categories = ['AWS']
+++

## Introdução

Neste artigo, abordaremos como criar uma frota de instâncias EC2 na AWS, configurar snapshots automatizados, criar um Load Balancer para distribuir o tráfego e implementar Auto Scaling para aumentar ou reduzir a capacidade automaticamente com base na demanda.

---

## 1️⃣ Criando uma Instância EC2

1. Acesse o **Console AWS** → **EC2**.
2. Clique em **Executar Instância**.
3. Escolha uma **Amazon Machine Image (AMI)**, como **Amazon Linux 2** ou **Ubuntu**.
4. Selecione um **tipo de instância** (exemplo: `t2.micro` para o Free Tier).
5. Configure a **rede e o Security Group**:
   - Permitir entrada na **porta 22** (SSH) para acesso remoto.
   - Permitir entrada na **porta 80** para acesso via navegador.
6. Clique em **Executar Instância**.

💡 **Após a criação, anote o IP da instância para testes.**

---

## 2️⃣ Criando uma AMI e Snapshots Automatizados

### Criando uma AMI personalizada

1. Vá até **EC2 → Instâncias**.
2. Selecione a instância criada.
3. Clique em **Ações → Imagens e Modelos → Criar Imagem**.
4. Nomeie a AMI e clique em **Criar**.

### Criando um Snapshot Manual

1. Vá até **EC2 → Volumes**.
2. Selecione o volume da instância.
3. Clique em **Ações → Criar Snapshot**.
4. Nomeie o snapshot e clique em **Criar**.

### Automatizando Snapshots (Lifecycle Manager)

1. Vá até **EC2 → Lifecycle Manager**.
2. Clique em **Criar Política**.
3. Escolha **EBS Snapshot Policy** e configure a periodicidade do backup (ex: a cada 24h).
4. Escolha os volumes e clique em **Criar**.

✅ Agora seus volumes terão **backups automáticos!**

---

## 3️⃣ Criando um Load Balancer (ALB)

1. Vá para **EC2 → Load Balancer**.
2. Clique em **Criar Load Balancer**.
3. Escolha **Application Load Balancer (ALB)**.
4. Configure:
   - **Nome:** `meu-load-balancer`
   - **Esquema:** Internet-facing
   - **Listeners:** HTTP na porta 80
   - **VPC e subnets:** Escolha **subnets públicas**
5. Criar um **Target Group**:
   - **Tipo:** Instâncias EC2
   - **Porta:** 80
   - **Adicione suas instâncias EC2**
6. Clique em **Criar Load Balancer**.

✅ Agora o tráfego será distribuído entre as instâncias!

---

## 4️⃣ Configurando o Auto Scaling Group

### Criando um Auto Scaling Group

1. Vá até **EC2 → Auto Scaling Groups**.
2. Clique em **Criar Auto Scaling Group**.
3. Escolha a **AMI criada anteriormente**.
4. Configure as regras de escalabilidade:
   - **Mínimo de instâncias:** 1
   - **Máximo de instâncias:** 2
   - **CPU Utilization > 50%:** Adicionar uma instância
   - **CPU Utilization < 20%:** Remover uma instância
5. Associe ao **Load Balancer criado anteriormente**.
6. Clique em **Criar Auto Scaling Group**.

✅ Agora suas instâncias **aumentarão ou diminuirão automaticamente!**

---

## 5️⃣ Testando a Escalabilidade

### Gerando Carga na Instância

Para testar o **Auto Scaling**, podemos simular uma alta carga de CPU na instância.

#### **Linux (Amazon Linux ou Ubuntu)**
```bash
sudo yum install -y stress
stress --cpu 2 --timeout 300  # 5 minutos de carga
```

#### **Windows (PowerShell)**
```powershell
for ($i=1; $i -le 1000; $i++) {
    Invoke-WebRequest -Uri "http://meuloadbalancer.amazonaws.com/" -UseBasicParsing
    Write-Host "Request #$i sent"
}
```

✅ Agora vá para **EC2 → Auto Scaling Groups** e **verifique se novas instâncias foram adicionadas!**

---

## Conclusão

Agora você tem uma frota de instâncias EC2 configurada com:
✅ **Snapshots automáticos** 📸  
✅ **Load Balancer distribuindo o tráfego** 🔄  
✅ **Auto Scaling ajustando instâncias dinamicamente** 📈📉  

🚀 **Agora sua aplicação está escalável, altamente disponível e segura!** 🔥

Se precisar de mais ajustes, deixe seu comentário! 📝

