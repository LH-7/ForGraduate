FINISH
/CLEAR
/FILNAME,SHISUAN1,1             !指定工作名称

/UNITS,SI                      !国际单位制
/PREP7
/NERR,,999999
!---------------------------------------------------------------------------------
!					PARA.
!---------------------------------------------------------------------------------

!钢轨中心水平距离
H=1.5


!****************************
!******轨道参数******!
!****************************
!轨枕
KJJJ=0.650                  !扣件间距/轨枕间距
SLAB_KJN=20                 !扣件数
SLEEPER_W=2.50              !轨枕宽度
SLEEPER_L=0.28              !轨枕长度
SLEEPER_H=0.23              !轨枕高度
H2=-0.02                    !轨枕高出道床板高度
H1=-0.25                    !钢轨距离轨枕表面距离

!道床板
SLAB_W=3.10          !轨道板宽度
SLAB_H=0.3         !轨道板高度
SLAB_F=0                             !板缝隙
KJJJ1=0.650                             !板缝处扣件间距
SLAB_BBZL=KJJJ/2             !板边距离第一组扣件距离
KJQD_L=SLAB_BBZL+SLAB_F                 !板缝隙+板边
SLAB_BBL=(SLAB_W-H)/2                   !板边宽度，H=1.5钢轨中心水平距离
SLAB_L=KJJJ*(SLAB_KJN-1)+2*SLAB_BBZL        !轨道板长度
BZ_L=SLAB_L-2*SLAB_BBZL                 !每块轨道板的第一组扣件和最后一组扣件的距离
SLAB_V=SLAB_L*SLAB_W*SLAB_H             !板体积

SLAB_N=1              !总轨道板数

!自密实混凝土
!CON_L=SLAB_L
!CON_H=0.100
!CON_W=SLAB_W

!底座板
BASE_L=SLAB_L
BASE_W=3.1
BASE_H=0.25
BASE_F=SLAB_F

!限位凹槽
AC_L=1.0
AC_W=0.70
AC_H=0.13
AC_N=4         !凹槽数
AC_JJ=3.5      !凹槽间距
AC_BD=(SLAB_L-AC_JJ*(AC_N-1))/2    !凹槽中心距离板端距离


!凹槽周围橡胶垫层
XJDC_H=0.007

HDJ=0.35                 !沥青混凝土表层弹簧长度
EDJ=190E6           !沥青混凝土表层面刚度


!钢轨节点单元参数
KJ_EN=4    !一跨扣件的钢轨单元数量
!轨道板单元数量
SLAB_EN=4    !一跨扣件的轨道板单元数量
BB_GG=4   !板边距钢轨的单元数量(横向)
BB_KJ1=SLAB_EN/2  !板边距第一组扣件的单元数量(纵向)

!轨道板边至底座板边单元数量
BASE_EN=2

!---------------------------------------------------------------------------------
!---------------------------------------------------------------------------------
!生成钢轨梁(BEAM188)
ET,1,188
MP,EX,1,2.06E11
MP,PRXY,1,0.3
MP,DENS,1,7800

!轨枕单元solid45
ET,100,SOLID45
MP,EX,100,3.60E10
MP,PRXY,100,0.2
MP,DENS,100,2500
MP,ALPX,100,1.0E-5

!道床板单元solid45
ET,2,SOLID45
MP,EX,2,3.60E10
MP,PRXY,2,0.2
MP,DENS,2,2500
MP,ALPX,2,1.0E-5

!自密实混凝土单元solid45
!ET,3,SOLID45
!MP,EX,3,3.25E10
!MP,PRXY,3,0.2
!MP,DENS,3,2500
!MP,ALPX,3,1.0E-5

!底座单元solid45
ET,4,SOLID45
MP,EX,4,3.25E10
MP,PRXY,4,0.2
MP,DENS,4,2500
MP,ALPX,4,1.0E-5

!定义扣件支承弹簧单元垂向（COMBIN14)
ET,5,COMBIN14,0,2,0
R,5,3.5E7
!定义扣件支承弹簧单元纵向（COMBIN14)
ET,6,COMBIN14,0,1,0
R,6,1.4E4
!定义扣件支承弹簧单元横向（COMBIN14)
ET,7,COMBIN14,0,3,0
R,7,5E7


!设定隔离层单元
GL_H=0.012
!土工布
ET,8,SOLID45
MP,EX,8,3.32E6
MP,PRXY,8,0.2
MP,DENS,8,175

!微孔橡胶垫
!ET,8,SOLID45
!MP,EX,8,3.70E6
!MP,PRXY,8,0.2
!MP,DENS,8,700

!弹性橡胶垫
!ET,8,SOLID45
!MP,EX,8,0.54E6
!MP,PRXY,8,0.2
!MP,DENS,8,481

!凹槽周围复合橡胶垫层
ET,9,SOLID45
MP,EX,9,50E6
MP,PRXY,9,0.2
MP,DENS,9,800


!设定桥面支承弹簧单元combin14
K_QM=5.0E9
ET,10,COMBIN14,0,2,0
R,10,K_QM

!定义接触单元
ET,20,170    !TARGE170
ET,21,173    !CONTAC173
R,20
MP,MU,20,0.2

!定义钢轨截面
SECTYPE,1,BEAM,MESH                                    !定义截面号为1
SECOFFSET,CENT
SECREAD,'60RAIL','SECT','',MESH                        !读取截面库文件

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!            钢轨部分                          !
!                                              !
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

!四根钢轨
R1=1000
R2=2000
RN=KJ_EN  !一扣件间距的钢轨单元数量

*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN*RN
    N,R1+SLAB_KJN*RN*(I-1)+J,SLAB_BBZL+(I-1)*(SLAB_L+SLAB_F)+(J-1)*KJJJ/RN,0,0
    N,R2+SLAB_KJN*RN*(I-1)+J,SLAB_BBZL+(I-1)*(SLAB_L+SLAB_F)+(J-1)*KJJJ/RN,0,-H
  *ENDDO
*ENDDO
!删除末尾节点
I1=SLAB_N
I2=SLAB_KJN*RN
BB1=I1*(SLAB_L+SLAB_F)-SLAB_F-SLAB_BBZL
NSEL,S,LOC,X,BB1+0.0001,BB1+1
NDELE,ALL

RND=10
N,RND,-1,5,0             !4方向点
N,RND+1,-1,5,-H

NUMBER=(SLAB_N*SLAB_KJN-1)*RN
TYPE,1
MAT,1
REAL,1
SECNUM,1
E,R1+1,R1+2,RND
*REPEAT,NUMBER,1,1,0

TYPE,1
MAT,1
REAL,1
SECNUM,1
E,R2+1,R2+2,RND+1
*REPEAT,NUMBER,1,1,0


!---------------------------------------------------------------------------------
!			     钢轨以下结构
!---------------------------------------------------------------------------------
!轨枕SLEEPER_CON
WPCSYS,-1
WPOFFS,-SLEEPER_L/2,,(SLEEPER_W-H)/2
BLC4,0,H1,SLEEPER_L,-SLEEPER_H,-SLEEPER_W

VSEL,S,LOC,Y,H1,H1-SLEEPER_H
VGEN,SLAB_KJN,ALL,,,KJJJ

VSEL,ALL
CM,SLEEPER_CONF,VOLU   !命名

!道床板SLAB_CON
WPCSYS,-1
WPOFFS,-SLAB_BBZL,,(SLAB_W-H)/2
BLC4,0,H1+H2,SLAB_L,-SLAB_H,-SLAB_W

VSEL,ALL
VSEL,U,,,SLEEPER_CONF
CM,SLAB_CON_1,VOLU

*DO,i,1,SLAB_KJN
  WPCSYS,-1
  WPOFFS,-SLEEPER_L/2+(i-1)*KJJJ,H1+H2,-(H-SLEEPER_W)/2
  BLC4,0,0,SLEEPER_L,-SLEEPER_H-H2,-SLEEPER_W
  VSEL,U,,,SLAB_CON_%i%
  CM,LH_%i%,VOLU
  VSEL,ALL
  VSEL,U,,,SLEEPER_CONF
  VSBV,SLAB_CON_%i%,LH_%i%
  VSEL,ALL
  VSEL,U,,,SLEEPER_CONF
  CM,SLAB_CON_%(i+1)%,VOLU
*ENDDO

VSEL,ALL
VGLUE,ALL             !粘接轨枕和道床板
CM,SLAB_CONF,VOLU     !轨枕加轨道板，命名与后面重复，需修改

!WPCSYS,-1
!WPOFFS,0,H1,-(H-SLEEPER_W)/2
!VSEL,ALL
!VSEL,S,LOC,X,-SLEEPER_L/2-0.0001,SLEEPER_L/2+0.0001
!*DO,i,2,SLAB_KJN
!  VSEL,A,LOC,X,-SLEEPER_L/2+(i-1)*KJJJ-0.0001,(i-1)*KJJJ+SLEEPER_L/2+0.0001
!*ENDDO
!VSEL,R,LOC,Y,-SLEEPER_H-0.0001,0.0001
!VSEL,R,LOC,Z,-SLEEPER_W-0.0001,0.0001
!CM,SLEEPER_CON,VOLU

!VSEL,ALL
!VSEL,U,,,SLEEPER_CON
!CM,SLAB_CONF,VOLU

!凹槽
WPCSYS,-1
WPOFFS,-SLAB_BBZL+(AC_BD-AC_L/2)+XJDC_H,H1+H2-SLAB_H,-(H-AC_W)/2-XJDC_H
BLC4,0,0,AC_L-2*XJDC_H,-AC_H,-(AC_W-2*XJDC_H)

VSEL,S,LOC,Y,H1-SLAB_H,H1-(SLAB_H+AC_H)
VGEN,4,ALL,,,AC_JJ

!VSEL,ALL
!VSEL,U,,,SLEEPER_CON
!VSEL,U,,,SLAB_CONF
!CM,TT,VOLU

VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VADD,ALL
CM,SLAB_CON,VOLU   !命名,轨枕+道床板+凸台


!底座板DZ
WPCSYS,-1
WPOFFS,-SLAB_BBZL,H1+H2-SLAB_H,(BASE_W-H)/2
BLC4,0,0,BASE_L,-(BASE_H+GL_H),-BASE_W
VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
CM,DZ1,VOLU

WPCSYS,-1
WPOFFS,-SLAB_BBZL+AC_BD-AC_L/2+XJDC_H,H1+H2-SLAB_H,-(H-AC_W)/2-XJDC_H
BLC4,0,0,AC_L-2*XJDC_H,-AC_H,-(AC_W-2*XJDC_H)
VSEL,U,,,DZ1
CM,AC1,VOLU

BLC4,AC_JJ,0,AC_L-2*XJDC_H,-AC_H,-(AC_W-2*XJDC_H)
VSEL,U,,,AC1
CM,AC2,VOLU

BLC4,2*AC_JJ,0,AC_L-2*XJDC_H,-AC_H,-(AC_W-2*XJDC_H)
VSEL,U,,,AC1
VSEL,U,,,AC2
CM,AC3,VOLU

BLC4,3*AC_JJ,0,AC_L-2*XJDC_H,-AC_H,-(AC_W-2*XJDC_H)
VSEL,U,,,AC1
VSEL,U,,,AC2
VSEL,U,,,AC3
CM,AC4,VOLU


VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
VSBV,DZ1,AC1       !第一个槽

VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
VSEL,U,,,AC2
CM,DZ2,VOLU

VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
VSBV,DZ2,AC2       !第二个槽

VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
VSEL,U,,,AC3
CM,DZ3,VOLU

VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
VSBV,DZ3,AC3       !第三个槽

VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
VSEL,U,,,AC4
CM,DZ4,VOLU

VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
VSBV,DZ4,AC4       !第四个槽



VSEL,ALL
!VSEL,U,,,SLEEPER_CON
VSEL,U,,,SLAB_CON
CM,DZ,VOLU

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!切分轨道结构
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!




!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!
!1沿厚度方向切分
!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!

!切分出轨枕
WPCSYS,-1
VSEL,ALL
WPOFFS,,H1+H2
WPROTA,,90
VSBW,ALL

WPOFFS,,,H2+SLEEPER_H
VSBW,ALL

!切分出道床板
WPOFFS,,,SLAB_H-(H2+SLEEPER_H)
VSBW,ALL

!切分出自密实混凝土
!WPOFFS,,,CON_H
!VSBW,ALL

!切分出隔离层
WPOFFS,,,GL_H
VSBW,ALL

!切分出凹槽
WPOFFS,,,AC_H-GL_H
VSBW,ALL

!切分出凹槽与底座之间的隔离层
WPOFFS,,,GL_H
VSBW,ALL

!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!
!2沿轨道横向切分
!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!

VSEL,ALL
WPCSYS,-1
WPOFFS,,,(BASE_W-H)/2

!切分出轨枕
WPOFFS,,,-(BASE_W-SLEEPER_W)/2
VSBW,ALL
WPOFFS,,,-SLEEPER_W
VSBW,ALL

!钢轨处切分
VSEL,ALL
WPCSYS,-1
VSBW,ALL
WPOFFS,,,-H
VSBW,ALL

!凹槽处切分
WPCSYS,-1
WPOFFS,,,-(H-AC_W)/2
VSBW,ALL
WPOFFS,,,-AC_W
VSBW,ALL

!凹槽处橡胶垫层切分
WPOFFS,,,XJDC_H
VSBW,ALL
WPOFFS,,,AC_W-2*XJDC_H
VSBW,ALL



!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!
!3沿轨道纵向切分
!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!

WPCSYS,-1
WPROTA,,,90
VSEL,ALL

!切分出扣件
WPOFFS,,,
VSBW,ALL
*DO,I,1,SLAB_KJN-1
  WPOFFS,,,KJJJ
  VSBW,ALL
*ENDDO

!切分出轨枕
*DO,I,1,SLAB_KJN
  *DO,J,1,2
    WPCSYS,-1
    WPROTA,,,90
    WPOFFS,,,-SLEEPER_L/2+(I-1)*KJJJ+(J-1)*SLEEPER_L
    VSBW,ALL
  *ENDDO
*ENDDO

!切分出凹槽
*DO,I,1,2
  *DO,J,1,4
    WPCSYS,-1
    WPROTA,,,90
    WPOFFS,,,-SLAB_BBZL+(AC_BD-AC_L/2)+(J-1)*AC_JJ+(I-1)*AC_L
    VSBW,ALL
  *ENDDO
*ENDDO

!切分出凹槽处橡胶垫层
*DO,I,1,2
  *DO,J,1,4
    WPCSYS,-1
    WPROTA,,,90
    WPOFFS,,,-SLAB_BBZL+(AC_BD-AC_L/2+XJDC_H)+(J-1)*AC_JJ+(I-1)*(AC_L-2*XJDC_H)
    VSBW,ALL
  *ENDDO
*ENDDO


!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!
!接触部分弹簧
!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!
!定义凹槽
WPCSYS,-1
ALLSEL,ALL
VSEL,ALL
VSEL,S,LOC,X,(AC_BD-AC_L/2)-0.0001,(AC_BD+AC_L/2)+0.0001
VSEL,A,LOC,X,(AC_BD-AC_L/2)+AC_JJ-0.0001,(AC_BD+AC_L/2)+AC_JJ+0.0001
VSEL,A,LOC,X,(AC_BD-AC_L/2)+2*AC_JJ-0.0001,(AC_BD+AC_L/2)+2*AC_JJ+0.0001
VSEL,A,LOC,X,(AC_BD-AC_L/2)+3*AC_JJ-0.0001,(AC_BD+AC_L/2)+3*AC_JJ+0.0001

VSEL,R,LOC,Z,-(H-AC_W)/2+0.0001,-(H-AC_W)/2-AC_W-0.0001
CM,AOCAO,VOLU

!定义左右橡胶垫
VSEL,S,,,AOCAO
VSEL,U,LOC,Z,-(H-AC_W)/2-XJDC_H-0.0001,-(H-AC_W)/2-AC_W+XJDC_H+0.0001
VSEL,R,LOC,Y,H1+H2-(SLAB_H)+0.0001,H1+H2-(SLAB_H+AC_H)-0.0001
CM,XJDC1,VOLU

!定义前后橡胶垫
VSEL,S,,,AOCAO
VSEL,U,,,XJDC1
VSEL,U,LOC,X,AC_BD-AC_L/2+XJDC_H-0.0001,AC_BD+AC_L/2-XJDC_H+0.0001
VSEL,U,LOC,X,AC_BD-AC_L/2+XJDC_H+AC_JJ-0.0001,AC_BD+AC_L/2-XJDC_H+AC_JJ+0.0001
VSEL,U,LOC,X,AC_BD-AC_L/2+XJDC_H+2*AC_JJ-0.0001,AC_BD+AC_L/2-XJDC_H+2*AC_JJ+0.0001
VSEL,U,LOC,X,AC_BD-AC_L/2+XJDC_H+3*AC_JJ-0.0001,AC_BD+AC_L/2-XJDC_H+3*AC_JJ+0.0001

VSEL,R,LOC,Y,H1+H2-SLAB_H-0.0001,H1+H2-(SLAB_H+AC_H)-0.0001
CM,XJDC2,VOLU

VSEL,ALL
VSEL,S,,,AOCAO
VSEL,U,,,XJDC1
VSEL,U,,,XJDC2
CM,AOCAO1,VOLU

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!划分网格!!!!
!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

!将不同长度的线分组
!沿线路纵向
LSEL,S,LENGTH,,XJDC_H-0.0001,XJDC_H+0.0001
LSEL,A,LENGTH,,GL_H-0.0001,GL_H+0.0001
CM,LZ1,LINE

LSEL,S,LENGTH,,SLEEPER_L/2-0.0001,SLEEPER_L/2+0.001
CM,LZ2,LINE

LSEL,S,LENGTH,,KJJJ-0.0001,KJJJ+0.0001
CM,LZ3,LINE

!沿线路横向
LSEL,S,LENGTH,,(SLAB_W-SLEEPER_W)/2-0.0001,(SLAB_W-SLEEPER_W)/2+0.0001
CM,LH1,LINE

LSEL,S,LENGTH,,(SLEEPER_W-H)/2-0.0001,(SLEEPER_W-H)/2+0.0001
CM,LH2,LINE

LSEL,S,LENGTH,,(H-AC_W)/2-XJDC_H-0.0001,(H-AC_W)/2+XJDC_H+0.0001
LSEL,U,,,LH2
CM,LH3,LINE

LSEL,S,LENGTH,,AC_W-2*XJDC_H-0.0001,AC_W-2*XJDC_H+0.0001
LSEL,U,,,LH2
LSEL,U,,,LH3
CM,LH4,LINE

!沿线路垂向
LSEL,S,LENGTH,,-H2-0.0001,-H2+0.0001
CM,LC1,LINE

LSEL,S,LENGTH,,SLEEPER_H+H2-0.0001,SLEEPER_H+H2+0.0001
CM,LC2,LINE

LSEL,S,LENGTH,,SLAB_H-SLEEPER_H-H2-0.0001,SLAB_H-SLEEPER_H-H2+0.0001
CM,LC3,LINE

LSEL,S,LENGTH,,AC_H-GL_H-0.0001,AC_H+GL_H+0.0001
CM,LC4,LINE

LSEL,S,LENGTH,,BASE_H-AC_H-GL_H-0.0001,BASE_H-AC_H+GL_H+0.0001
CM,LC5,LINE


!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!预划分
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!沿线路纵向
LSEL,S,,,LZ1
LESIZE,ALL,,,1

LSEL,S,,,LZ2
LESIZE,ALL,,,2

LSEL,S,,,LZ3
LESIZE,ALL,,,4

!沿线路横向
LSEL,S,,,LH1
LESIZE,ALL,,,2

LSEL,S,,,LH2
LSEL,A,,,LH3
LSEL,A,,,LH4
LESIZE,ALL,,,4



!!!!!横向



LSEL,S,LENGTH,,AC_W-2*XJDC_H-0.0001,AC_W-2*XJDC_H+0.0001

LESIZE,ALL,,,1




!沿线路垂向
LSEL,S,,,LC2
LSEL,A,,,LC4
LSEL,A,,,LC5
LESIZE,ALL,,,4

LSEL,S,,,LC1
LSEL,A,,,LC4
LESIZE,ALL,,,2

LSEL,ALL
LESIZE,ALL,,,1



!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!划分网格
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
ALLSEL,ALL

!凹槽部分
!轨枕
VSEL,S,,,AOCAO1
VSEL,R,LOC,Y,H1+0.0001,H1-SLEEPER_H-0.0001
TYPE,100
MAT,100
MSHAPE,0
MSHKEY,2
SECNUM,100
VMESH,ALL

!道床板
VSEL,S,,,AOCAO1
VSEL,R,LOC,Y,H1-SLEEPER_H+0.0001,H1+H2-SLAB_H-0.0001
TYPE,2
MAT,2
MSHAPE,0
MSHKEY,2
SECNUM,2
VMESH,ALL


!隔离层
VSEL,S,,,AOCAO1
VSEL,R,LOC,Y,H1+H2-SLAB_H-AC_H-0.0001,H1+H2-SLAB_H-AC_H-GL_H-0.0001
TYPE,8
MAT,8
SECNUM,8
VMESH,ALL

!底座板
VSEL,S,,,AOCAO1
VSEL,R,LOC,Y,H1+H2-SLAB_H-AC_H-GL_H-0.0001,H1+H2-SLAB_H-AC_H-GL_H-BASE_H-0.0001
TYPE,4
MAT,4
SECNUM,4
VMESH,ALL

!橡胶垫层
VSEL,S,,,XJDC1
VSEL,A,,,XJDC2
TYPE,9
MAT,9
SECNUM,9
VMESH,ALL

!非凹槽部分
!轨枕
WPCSYS,-1
WPOFFS,0,0,(SLEEPER_W-H)/2
VSEL,S,,,AOCAO
VSEL,INVE                      !INVE反向选择
VSEL,R,LOC,Z,-SLEEPER_W-0.0001,-(SLEEPER_W+AC_W)/2+0.0001
VSEL,A,LOC,Z,-(SLEEPER_W-AC_W)/2-0.0001,0.0001
VSEL,R,LOC,Y,H1+0.0001,H1-SLEEPER_H-0.0001
TYPE,100
MAT,100
MSHAPE,0
MSHKEY,2
SECNUM,100
VMESH,ALL


!道床板
WPCSYS,-1
WPOFFS,0,0,(SLEEPER_W-H)/2
VSEL,S,,,AOCAO
VSEL,INVE                      !INVE反向选择
VSEL,R,LOC,Z,-SLEEPER_W-0.0001,-(SLEEPER_W+AC_W)/2+0.0001
VSEL,A,LOC,Z,-(SLEEPER_W-AC_W)/2-0.0001,0.0001
VSEL,R,LOC,Y,H1-SLEEPER_H+0.0001,H1+H2-SLAB_H-0.0001
TYPE,2
MAT,2
SECNUM,2
VMESH,ALL

WPCSYS,-1
WPOFFS,0,0,(SLAB_W-H)/2
VSEL,S,,,AOCAO
VSEL,INVE                      !INVE反向选择
VSEL,R,LOC,Z,-SLAB_W-0.0001,-(SLEEPER_W+SLAB_W)/2+0.0001
VSEL,A,LOC,Z,-(SLAB_W-SLEEPER_W)/2-0.0001,0.0001
VSEL,R,LOC,Y,H1+H2+0.0001,H1+H2-SLAB_H-0.0001
TYPE,2
MAT,2
SECNUM,2
VMESH,ALL

WPCSYS,-1
WPOFFS,0,0,0
!隔离层
VSEL,S,,,AOCAO
VSEL,INVE
VSEL,R,LOC,Y,H1+H2-SLAB_H-0.0001,H1+H2-SLAB_H-GL_H-0.0001
TYPE,8
MAT,8
SECNUM,8
VMESH,ALL

!底座
VSEL,S,,,AOCAO
VSEL,INVE
VSEL,R,LOC,Y,H1+H2-SLAB_H-GL_H-0.0001,H1+H2-SLAB_H-GL_H-BASE_H-0.0001
TYPE,4
MAT,4
SECNUM,4
VMESH,ALL



!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!接触分析
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

!这的接触分析中，首先考虑的是自密实混凝土和底座板之间的接触，竖向的接触，限位凸台、自密实混凝土和底座板之间的竖向接触，即隔离层接触
!然后是考虑限位凸台四周的接触，只算是橡胶垫层内侧和凸台外侧的接触，即橡胶垫接触

!隔离层接触
!凹槽部分
!目标面
VSEL,ALL
VSEL,S,,,AOCAO
VSEL,R,LOC,Y,H1+H2-SLAB_H-0.0001,H1+H2-SLAB_H-AC_H-0.0001
ASLV,S
BB1=H1+H2-SLAB_H-AC_H
ASEL,R,LOC,Y,BB1-0.0001,BB1+0.0001
NSLA,S,1
REAL,20
MAT,20
TYPE,20
ESURF,ALL

!接触面
VSEL,ALL
VSEL,S,,,AOCAO
VSEL,R,LOC,Y,BB1+0.0001,BB1-GL_H-0.0001
ASLV,S
ASEL,R,LOC,Y,BB1-0.0001,BB1+0.0001
NSLA,S,1
REAL,20
TYPE,21
ESURF,ALL

!非凹槽部分
!目标面
VSEL,ALL
VSEL,S,,,AOCAO
VSEL,INVE
VSEL,R,LOC,Y,H1+H2+0.0001,H1+H2-SLAB_H-0.0001
ASLV,S
ASEL,R,LOC,Y,H1+H2-SLAB_H-0.0001,H1+H2-SLAB_H+0.0001
NSLA,S,1
REAL,20
MAT,20
TYPE,20
ESURF,ALL

!接触面
VSEL,ALL
VSEL,S,,,AOCAO
VSEL,INVE
VSEL,R,LOC,Y,H1+H2-SLAB_H-0.0001,H1+H2-SLAB_H-BASE_H-0.0001
ASLV,S
ASEL,R,LOC,Y,H1+H2-SLAB_H-0.0001,H1+H2-SLAB_H+0.0001
NSLA,S,1
REAL,20
TYPE,21
ESURF,ALL

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!橡胶垫接触
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!目标面
VSEL,ALL
VSEL,S,,,AOCAO
VSEL,U,,,XJDC1
VSEL,U,,,XJDC2
VSEL,R,LOC,Y,H1+H2-SLAB_H+0.0001,H1+H2-(SLAB_H+AC_H)-0.0001
ASLV,S
ASEL,U,LOC,Z,-(H-AC_W)/2-XJDC_H-0.0001,-(H-AC_W)/2-XJDC_H-(AC_W-2*XJDC_H)+0.0001
CM,A1,AREA

VSEL,ALL
VSEL,S,,,AOCAO
VSEL,U,,,XJDC1
VSEL,U,,,XJDC2
VSEL,R,LOC,Y,H1+H2-SLAB_H+0.0001,H1+H2-(SLAB_H+AC_H)-0.0001
ASLV,S
ASEL,U,LOC,X,AC_BD-AC_L/2+XJDC_H+0.0001,AC_BD+AC_L/2-XJDC_H-0.0001
ASEL,U,LOC,X,AC_BD-AC_L/2+XJDC_H+AC_JJ+0.0001,AC_BD+AC_L/2-XJDC_H+AC_JJ-0.0001
ASEL,U,LOC,X,AC_BD-AC_L/2+XJDC_H+2*AC_JJ+0.0001,AC_BD+AC_L/2-XJDC_H+2*AC_JJ-0.0001
ASEL,U,LOC,X,AC_BD-AC_L/2+XJDC_H+3*AC_JJ+0.0001,AC_BD+AC_L/2-XJDC_H+3*AC_JJ-0.0001

CM,A2,AREA


ASEL,S,,,A1
ASEL,A,,,A2
NSLA,S,1
REAL,20
MAT,20
TYPE,20
ESURF,ALL


!接触面
VSEL,ALL
VSEL,S,,,XJDC1
!垫层
VSEL,U,LOC,X,AC_BD-AC_L/2+0.0001,AC_BD-AC_L/2+XJDC_H+0.0001
VSEL,U,LOC,X,AC_BD+AC_L/2-XJDC_H-0.0001,AC_BD+AC_L/2+0.0001

VSEL,U,LOC,X,AC_BD-AC_L/2+AC_JJ+0.0001,AC_BD-AC_L/2+XJDC_H+AC_JJ+0.0001
VSEL,U,LOC,X,AC_BD+AC_L/2-XJDC_H+AC_JJ-0.0001,AC_BD+AC_L/2+AC_JJ+0.0001

VSEL,U,LOC,X,AC_BD-AC_L/2+2*AC_JJ+0.0001,AC_BD-AC_L/2+XJDC_H+2*AC_JJ+0.0001
VSEL,U,LOC,X,AC_BD+AC_L/2-XJDC_H+2*AC_JJ-0.0001,AC_BD+AC_L/2+2*AC_JJ+0.0001

VSEL,U,LOC,X,AC_BD-AC_L/2+3*AC_JJ+0.0001,AC_BD-AC_L/2+XJDC_H+3*AC_JJ+0.0001
VSEL,U,LOC,X,AC_BD+AC_L/2-XJDC_H+3*AC_JJ-0.0001,AC_BD+AC_L/2+3*AC_JJ+0.0001

ASLV,S
ASEL,R,LOC,Z,-(H-AC_W)/2-XJDC_H+0.0001,-(H-AC_W)/2-XJDC_H-(AC_W-2*XJDC_H)-0.0001
CM,A11,AREA


!凸台
!每块第一个
VSEL,ALL
VSEL,S,,,XJDC2
ASLV,S
ASEL,R,LOC,X,AC_BD-AC_L/2+XJDC_H-0.0001,AC_BD+AC_L/2-XJDC_H+0.0001
CM,A21,AREA


!每块第二个
VSEL,ALL
VSEL,S,,,XJDC2
ASLV,S
ASEL,R,LOC,X,AC_BD-AC_L/2+XJDC_H+AC_JJ-0.0001,AC_BD+AC_L/2-XJDC_H+AC_JJ+0.0001
CM,A22,AREA

!每块第三个
VSEL,ALL
VSEL,S,,,XJDC2
ASLV,S
ASEL,R,LOC,X,AC_BD-AC_L/2+XJDC_H+2*AC_JJ-0.0001,AC_BD+AC_L/2-XJDC_H+2*AC_JJ+0.0001
CM,A23,AREA

!每块第四个
VSEL,ALL
VSEL,S,,,XJDC2
ASLV,S
ASEL,R,LOC,X,AC_BD-AC_L/2+XJDC_H+3*AC_JJ-0.0001,AC_BD+AC_L/2-XJDC_H+3*AC_JJ+0.0001
CM,A24,AREA


ASEL,S,,,A11
ASEL,A,,,A21
ASEL,A,,,A22
ASEL,A,,,A23
ASEL,A,,,A24

NSLA,S,1
REAL,20
TYPE,21
ESURF,ALL


!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!扣件!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
CSYS,0
!左股钢轨扣件
ALLSEL,ALL
*DIM,ZRQJ,ARRAY,SLAB_N,SLAB_KJN
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
     NSEL,S,LOC,Y,0
     NSEL,R,LOC,Z,0
     AA=SLAB_BBZL+(I-1)*(SLAB_L+SLAB_F)+KJJJ*(J-1)
     NSEL,R,LOC,X,AA-0.0001,AA+0.0001
     *GET,KJNIJ,NODE,,NUM,MAX
     ZRQJ(I,J)=KJNIJ
  *ENDDO
*ENDDO

!右股钢轨扣件
ALLSEL,ALL
*DIM,YRQJ,ARRAY,SLAB_N,SLAB_KJN
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
    NSEL,S,LOC,Y,0
    NSEL,R,LOC,Z,-H
    AA1=0+SLAB_BBZL+(I-1)*(SLAB_L+SLAB_F)+KJJJ*(J-1)
    NSEL,R,LOC,X,AA1-0.0001,AA1+0.0001
    *GET,KJNIJ,NODE,,NUM,MAX
    YRQJ(I,J)=KJNIJ
  *ENDDO
*ENDDO

!轨道板左侧扣件节点
ALLSEL,ALL
*DIM,ZBQJ,ARRAY,SLAB_N,SLAB_KJN
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
    NSEL,S,LOC,Y,H1
    NSEL,R,LOC,Z,0
    AA1=SLAB_BBZL+(I-1)*(SLAB_L+SLAB_F)+KJJJ*(J-1)
    NSEL,R,LOC,X,AA1-0.0001,AA1+0.0001
    *GET,KJNIJ,NODE,,NUM,MAX
    ZBQJ(I,J)=KJNIJ
  *ENDDO
*ENDDO

!轨道板右侧扣件节点
ALLSEL,ALL
*DIM,YBQJ,ARRAY,SLAB_N,SLAB_KJN
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
    NSEL,S,LOC,Y,H1
    NSEL,R,LOC,Z,-H
    AA1=SLAB_BBZL+(I-1)*(SLAB_L+SLAB_F)+KJJJ*(J-1)
    NSEL,R,LOC,X,AA1-0.0001,AA1+0.0001
    *GET,KJNIJ,NODE,,NUM,MAX
    YBQJ(I,J)=KJNIJ
  *ENDDO
*ENDDO

!生成扣件垂向
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
    TYPE,5
    REAL,5
    SECNUM,5
    E,ZRQJ(I,J),ZBQJ(I,J)
    E,YRQJ(I,J),YBQJ(I,J)
  *ENDDO
*ENDDO
!生成扣件纵向
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
    TYPE,6
    REAL,6
    SECNUM,6
    E,ZRQJ(I,J),ZBQJ(I,J)
    E,YRQJ(I,J),YBQJ(I,J)
  *ENDDO
*ENDDO
!生成扣件横向
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
    TYPE,7
    REAL,7
    SECNUM,7
    E,ZRQJ(I,J),ZBQJ(I,J)
    E,YRQJ(I,J),YBQJ(I,J)
  *ENDDO
*ENDDO


!!!!!!!!!!!!!!生成地基弹簧!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
WPCSYS,1
ALLSEL,ALL
NSEL,S,LOC,Y,H1+H2-SLAB_H-(BASE_H+GL_H)
*GET,ND,NODE,ALL,COUNT
CM,DZ,NODE
NGEN,2,900000,DZ,,,,-HDJ
ET,30,COMBIN14,0,2,0
KEYOPT,30,2,2
R,30,EDJ*BASE_L*BASE_W/ND
NSEL,S,LOC,Y,H1+H2-SLAB_H-(BASE_H+GL_H)
NSEL,A,LOC,Y,,H1+H2-SLAB_H-(BASE_H+GL_H)-HDJ
TYPE,30
REAL,30
EINTF,0.00001,,LOW,,,HDJ
FINISH






save















!约束和加载

/SOLU
CSYS,0
ALLSEL,ALL


!地基弹簧下侧约束
ALLSEL,ALL
NSEL,S,LOC,Y,H1-(SLAB_H+QM_H)-(BASE_H+GL_H)-HDJ
D,ALL,ALL


!钢轨两端约束
ALLSEL,ALL
NSEL,S,LOC,X,SLAB_BBZL-0.0001,SLAB_BBZL+0.0001
ZJXU=SLAB_N*(SLAB_L+SLAB_F)-SLAB_F-SLAB_BBZL
NSEL,A,LOC,X,ZJXU-0.0001,ZJXU+0.0001
NSEL,R,LOC,Y,0
D,ALL,ALL


!约束底座两端
ALLSEL,ALL
NSEL,S,LOC,X,-0.0001,0.0001
NSEL,A,LOC,X,SLAB_N*(SLAB_L+SLAB_F)-SLAB_F-0.0001,SLAB_N*(SLAB_L+SLAB_F)-SLAB_F+0.0001
NSEL,R,LOC,Y,H1-(SLAB_H)-GL_H+0.0001,H1-(SLAB_H)-(BASE_H+GL_H)-0.0001
D,ALL,UX


!加载

!伸缩力
!轨道板左侧扣件节点
ALLSEL,ALL
*DIM,ZBQJ,ARRAY,SLAB_N,SLAB_KJN
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
    NSEL,S,LOC,Y,H1
    NSEL,R,LOC,Z,0
    AA1=SLAB_BBZL+(I-1)*(SLAB_L+SLAB_F)+KJJJ*(J-1)
    NSEL,R,LOC,X,AA1-0.0001,AA1+0.0001
    F,ALL,FX,-38800
  *ENDDO
*ENDDO

!轨道板右侧扣件节点
ALLSEL,ALL
*DIM,YBQJ,ARRAY,SLAB_N,SLAB_KJN
*DO,I,1,SLAB_N
  *DO,J,1,SLAB_KJN
    NSEL,S,LOC,Y,H1
    NSEL,R,LOC,Z,-H
    AA1=SLAB_BBZL+(I-1)*(SLAB_L+SLAB_F)+KJJJ*(J-1)
    NSEL,R,LOC,X,AA1-0.0001,AA1+0.0001
    F,ALL,FX,-38800
  *ENDDO
*ENDDO


!加温度梯度荷载
ALLSEL,ALL
TREF,0

*DO,I,1,3
  NSEL,S,LOC,Y,H1-(I-1)*SLAB_H/2
  BF,ALL,TEMP,44*(I-1)*SLAB_H/2+35
*ENDDO




!求解
ANTYPE,STATIC
NLGEOM,ON
AUTOTS,ON
ACEL,,9.81
TIME,1.0
NSUBST,100
OUTRES,ALL,ALL
ALLSEL,ALL
SOLVE
FINISH
