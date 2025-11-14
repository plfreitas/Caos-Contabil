# Caos-Cont-bil

# 🏦 O Cofre do Banco: Correção de Condição de Corrida com Mutex (Windows API)

## 🎯 Objetivo do Projeto

Demonstrar a identificação, compreensão e correção de uma **Condição de Corrida (Race Condition)** em um programa C multithread, utilizando o mecanismo de sincronização **Mutex** da API do Windows.

## 🐛 O Problema (Código "Quebrado")

O código-base simula 5 threads (agências) realizando depósitos e saques em uma variável global (`g_saldoBancario`). A presença de um `Sleep()` nas funções de transação expõe a vulnerabilidade: threads liam saldos desatualizados e sobrescreviam o trabalho umas das outras, resultando em um saldo final incorreto e imprevisível.

**Cálculo Esperado:**
`1000 (Inicial) + 500 - 300 + 200 - 700 + 100 = 800.00`

## ✅ A Solução Implementada

A correção foi realizada através da implementação de um **Mutex** para garantir a **Exclusão Mútua** no acesso ao recurso compartilhado.

| Componente | Função | Implementação |
| :--- | :--- | :--- |
| **Recurso Protegido** | `g_saldoBancario` (Saldo Global) | Variável global acessada por todas as threads. |
| **Mutex** | `HANDLE hMutex` | Variável global para o objeto Mutex. |
| **Inicialização** | `CreateMutex(NULL, FALSE, NULL)` | Chamado na `main()` para criar o Mutex. |
| **Entrada na SC** | `WaitForSingleObject(hMutex, INFINITE)` | Chamado no início das funções `operacaoDeposito` e `operacaoSaque`. |
| **Saída da SC** | `ReleaseMutex(hMutex)` | Chamado após a atualização do saldo global. |
| **Limpeza** | `CloseHandle(hMutex)` | Chamado no final da `main()` para liberar o recurso. |

## 🚀 Resultado Comprovado

Após a correção, o programa sempre finaliza com o saldo exato, independentemente da ordem de execução das threads:

