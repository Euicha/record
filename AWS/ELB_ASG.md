# ELB + ASG
## Scalability & High Availability
Scalability(확장성)은 애플리케이션 / 시스템이 조정을 통해 더 많은 양을 처리할 수 있다는 의미.  
- Vertical Scalability  
- Horizontal Scalability  
  
There's usually a limit to how much you can vertically scale (hardware limit)  
  
Horizontal scaling implies distributed systems.  
  
High availability는 애플리케이션 또는 시스템을 적어도 둘 이상의 AWS 의 AZ나 데이터 센터에서 가동 중이라는 걸 의미한다.  
고가용성의 목표는 데이터 센터에서의 손실에서 살아남는 것. 센터 하나가 멈춰도 계속 작동이 가능하게끔 하는 것.  
```
고가용성의 목표는 데이터 센터에서의 손실에서 살아남는 것.  
  
핵심은 리소스가 2개 이상의 AZ에 분산되어 있는 것이며,  
ECS처럼 서브넷을 직접 지정하는 서비스는 서로 다른 AZ의 서브넷을  
2개 이상 지정해야 한다.
```
  
High Availability can be passive (for RDS Multi AZ for example), passive는 "평소엔 놀고 있지만 장애 시 즉시 대체 가능한 구조"  
High Availability can be active (for horizontal scaling).  
  
수평 확장은 인스턴스의 수를 늘린다는 것. Scale Out / Scale In. 인스턴스의 수가 늘어나면 스케일 아웃이고 줄어들면 스케일 인이다.  
  
AWS의 고가용성은 동일 애플리케이션의 동일 인스턴스를 다수의 AZ에 걸쳐 실행하는 경우를 의미한다.  
