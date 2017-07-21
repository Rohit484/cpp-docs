---
title: "How to: Use Schedule Groups to Influence Order of Execution | Microsoft Docs"
ms.custom: ""
ms.date: "11/04/2016"
ms.reviewer: ""
ms.suite: ""
ms.technology:  
  - "cpp-windows"
ms.tgt_pltfrm: ""
ms.topic: "article"
dev_langs: 
  - "C++"
helpviewer_keywords: 
  - "schedule groups, using [Concurrency Runtime]"
  - "using schedule groups [Concurrency Runtime]"
ms.assetid: 73124194-fc3a-491e-a23f-fbd7b5a4455c
caps.latest.revision: 15
author: "mikeblome"
ms.author: "mblome"
manager: "ghogen"
translation.priority.ht: 
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "ru-ru"
  - "zh-cn"
  - "zh-tw"
translation.priority.mt: 
  - "cs-cz"
  - "pl-pl"
  - "pt-br"
  - "tr-tr"
---
# How to: Use Schedule Groups to Influence Order of Execution
In the Concurrency Runtime, the order in which tasks are scheduled is non-deterministic. However, you can use scheduling policies to influence the order in which tasks run. This topic shows how to use schedule groups together with the [concurrency::SchedulingProtocol](reference/concurrency-namespace-enums.md#policyelementkey) scheduler policy to influence the order in which tasks run.  

  
 The example runs a set of tasks two times, each with a different scheduling policy. Both policies limit the maximum number of processing resources to two. The first run uses the `EnhanceScheduleGroupLocality` policy, which is the default, and the second run uses the `EnhanceForwardProgress` policy. Under the `EnhanceScheduleGroupLocality` policy, the scheduler runs all tasks in one schedule group until each task finishes or yields. Under the `EnhanceForwardProgress` policy, the scheduler moves to the next schedule group in a round-robin manner after just one task finishes or yields.  
  
 When each schedule group contains related tasks, the `EnhanceScheduleGroupLocality` policy typically results in improved performance because cache locality is preserved between tasks. The `EnhanceForwardProgress` policy enables tasks to make forward progress and is useful when you require scheduling fairness across schedule groups.  
  
## Example  
 This example defines the `work_yield_agent` class, which derives from [concurrency::agent](../../parallel/concrt/reference/agent-class.md). The `work_yield_agent` class performs a unit of work, yields the current context, and then performs another unit of work. The agent uses the [concurrency::wait](reference/concurrency-namespace-functions.md#wait) function to cooperatively yield the current context so that other contexts can run.  
  
 This example creates four `work_yield_agent` objects. To illustrate how to set scheduler policies to affect the order in which the agents run, the example associates the first two agents with one schedule group and the other two agents with another schedule group. The example uses the [concurrency::CurrentScheduler::CreateScheduleGroup](reference/currentscheduler-class.md#createschedulegroup) method to create the [concurrency::ScheduleGroup](../../parallel/concrt/reference/schedulegroup-class.md) objects. The example runs all four agents two times, each time with a different scheduling policy.  
  
 [!code-cpp[concrt-scheduling-protocol#1](../../parallel/concrt/codesnippet/cpp/how-to-use-schedule-groups-to-influence-order-of-execution_1.cpp)]  
  
 This example produces the following output.  
  
```Output  
Using EnhanceScheduleGroupLocality...  
group 0,
    task 0: first loop...  
group 0,
    task 1: first loop...  
group 0,
    task 0: waiting...  
group 1,
    task 0: first loop...  
group 0,
    task 1: waiting...  
group 1,
    task 1: first loop...  
group 1,
    task 0: waiting...  
group 0,
    task 0: second loop...  
group 1,
    task 1: waiting...  
group 0,
    task 1: second loop...  
group 0,
    task 0: finished...  
group 1,
    task 0: second loop...  
group 0,
    task 1: finished...  
group 1,
    task 1: second loop...  
group 1,
    task 0: finished...  
group 1,
    task 1: finished...  
 
Using EnhanceForwardProgress...  
group 0,
    task 0: first loop...  
group 1,
    task 0: first loop...  
group 0,
    task 0: waiting...  
group 0,
    task 1: first loop...  
group 1,
    task 0: waiting...  
group 1,
    task 1: first loop...  
group 0,
    task 1: waiting...  
group 0,
    task 0: second loop...  
group 1,
    task 1: waiting...  
group 1,
    task 0: second loop...  
group 0,
    task 0: finished...  
group 0,
    task 1: second loop...  
group 1,
    task 0: finished...  
group 1,
    task 1: second loop...  
group 0,
    task 1: finished...  
group 1,
    task 1: finished...  
```  
  
 Both policies produce the same sequence of events. However, the policy that uses `EnhanceScheduleGroupLocality` starts both agents that are part of the first schedule group before it starts the agents that are part of the second group. The policy that uses `EnhanceForwardProgress` starts one agent from the first group and then starts the first agent in the second group.  
  
## Compiling the Code  
 Copy the example code and paste it in a Visual Studio project, or paste it in a file that is named `scheduling-protocol.cpp` and then run the following command in a Visual Studio Command Prompt window.  
  
 **cl.exe /EHsc scheduling-protocol.cpp**  
  
## See Also  
 [Schedule Groups](../../parallel/concrt/schedule-groups.md)   
 [Asynchronous Agents](../../parallel/concrt/asynchronous-agents.md)
