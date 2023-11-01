---
title: FluentValidation在C# WPF中的应用
slug: Uses-fluent-validation-in-WPF
description: 介绍FluentValidation的文章不少，其实它也可以用于WPF属性验证，本文主要也是讲解该组件在WPF中的使用
date: 2019-11-19 03:43:13
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2019/11/cover_01.png
categories: WPF
copyright: Original
---

## 1. 简介

介绍FluentValidation的文章不少，[零度编程](https://www.xcode.me/post/5849)的介绍我引用下：

>FluentValidation 是一个基于 .NET 开发的验证框架，开源免费，而且优雅，支持链式操作，易于理解，功能完善，还是可与 MVC5、WebApi2 和 ASP.NET CORE 深度集成，组件内提供十几种常用验证器，可扩展性好，支持自定义验证器，支持本地化多语言。

其实它也可以用于WPF属性验证，本文主要也是讲解该组件在WPF中的使用，FluentValidation官网是： https://fluentvalidation.net/ 。

## 2. 本文需要实现的功能

提供WPF界面输入验证，采用MVVM方式，需要以下功能：

1. 能验证ViewModel中定义的简单属性；
2. 能验证ViewModel中定义的复杂属性，比如对象属性的子属性，如VM有个学生属性Student,需要验证他的姓名、年龄等；
3. 能简单提供两种验证样式；
4. 没有了，就是前面3点…

先看实现效果图：

![](https://img1.dotnet9.com/2019/11/0101.png)

## 3. 调研中遇到的问题

简单属性：验证ViewModel的普通属性比较简单，可以参考[FluentValidation官网](https://fluentvalidation.net/) ，或者国外holymoo大神的代码： [UserValidator.cs](https://gist.github.com/holymoo/11243164) 。

复杂属性：我遇到的问题是，怎么验证ViewModel中对象属性的子属性？见第二个功能描述，[FluentValidation官网](https://fluentvalidation.net/)有[Complex Properties](https://fluentvalidation.net/start#complex-properties)的例子，但是我试了没效果，贴上官方源码截图：

![](https://img1.dotnet9.com/2019/11/0102.png)

最后我Google到这篇文章，根据该[链接代码](https://adityaswami89.wordpress.com/category/xaml/wpf/)，ViewModel和子属性都实现IDataErrorInfo接口，即可实现复杂属性验证，文章中没有具体实现，但灵感是从这来的，就不具体说该链接代码了，有兴趣可以点击链接阅读，下面贴上代码。

## 4. 开发步骤

1. 创建工程、引入库

创建.Net Core WPF模板解决方案（.Net Framework模板也行）：WpfFluentValidation，引入Nuget包FluentValidation(8.5.1)。

2. 创建测试实体类学生：Student.cs

此类用作ViewModel中的复杂属性使用，学生类包含3个属性：名字、年龄、邮政编码。此实体需要继承IDataErrorInfo接口，触发FluentValidation必须的。

```C#
using System.ComponentModel;
using System.Linq;
using WpfFluentValidation.Validators;

namespace WpfFluentValidation.Models
{
    /// <summary>
    /// 学生实体
    /// 继承BaseClasss,即继承属性变化接口INotifyPropertyChanged
    /// 实现IDataErrorInfo接口，用于FluentValidation验证，必须实现此接口
    /// </summary>
    public class Student : BaseClass, IDataErrorInfo
    {
        private string name;
        public string Name
        {
            get { return name; }
            set
            {
                if (value != name)
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }
        }
        private int age;
        public int Age
        {
            get { return age; }
            set
            {
                if (value != age)
                {
                    age = value;
                    OnPropertyChanged(nameof(Age));
                }
            }
        }
        private string zip;
        public string Zip
        {
            get { return zip; }
            set
            {
                if (value != zip)
                {
                    zip = value;
                    OnPropertyChanged(nameof(Zip));
                }
            }
        }

        public string Error { get; set; }

        public string this[string columnName]
        {
            get
            {
                if (validator == null)
                {
                    validator = new StudentValidator();
                }
                var firstOrDefault = validator.Validate(this)
                    .Errors.FirstOrDefault(lol => lol.PropertyName == columnName);
                return firstOrDefault?.ErrorMessage;
            }
        }

        private StudentValidator validator { get; set; }
    }
}
```

3. 创建学生验证器：StudentValidator.cs

验证属性的写法有两种：

1. 可以在实体属性上方添加特性（本文不作特别说明，百度文章介绍很多）；
2. 通过代码的形式添加，如下方，创建一个验证器类，继承自AbstractValidator，在此验证器构造函数中写规则验证属性，方便管理。

本文使用第二种，见下方学生验证器代码：

```C#
using FluentValidation;
using System.Text.RegularExpressions;
using WpfFluentValidation.Models;

namespace WpfFluentValidation.Validators
{
    public class StudentValidator : AbstractValidator<Student>
    {
        public StudentValidator()
        {
            RuleFor(vm => vm.Name)
                    .NotEmpty()
                    .WithMessage("请输入学生姓名！")
                .Length(5, 30)
                .WithMessage("学生姓名长度限制在5到30个字符之间！");

            RuleFor(vm => vm.Age)
                .GreaterThanOrEqualTo(0)
                .WithMessage("学生年龄为整数！")
                .ExclusiveBetween(10, 150)
                .WithMessage($"请正确输入学生年龄(10-150)");

            RuleFor(vm => vm.Zip)
                .NotEmpty()
                .WithMessage("邮政编码不能为空！")
                .Must(BeAValidZip)
                .WithMessage("邮政编码由六位数字组成。");
        }

        private static bool BeAValidZip(string zip)
        {
            if (!string.IsNullOrEmpty(zip))
            {
                var regex = new Regex(@"\d{6}");
                return regex.IsMatch(zip);
            }
            return false;
        }
    }
}
```

4. 创建ViewModel类：StudentViewModel.cs

StudentViewModel与Student实体类结构类似，都需要实现IDataErrorInfo接口，该类由一个简单的string属性(Title)和一个复杂的Student对象属性(CurrentStudent)组成，代码如下：

```C#
using System;
using System.ComponentModel;
using System.Linq;
using WpfFluentValidation.Models;
using WpfFluentValidation.Validators;

namespace WpfFluentValidation.ViewModels
{
    /// <summary>
    /// 视图ViewModel
    /// 继承BaseClasss,即继承属性变化接口INotifyPropertyChanged
    /// 实现IDataErrorInfo接口，用于FluentValidation验证，必须实现此接口
    /// </summary>
    public class StudentViewModel : BaseClass, IDataErrorInfo
    {
        private string title;
        public string Title
        {
            get { return title; }
            set
            {
                if (value != title)
                {
                    title = value;
                    OnPropertyChanged(nameof(Title));
                }
            }
        }

        private Student currentStudent;
        public Student CurrentStudent
        {
            get { return currentStudent; }
            set
            {
                if (value != currentStudent)
                {
                    currentStudent = value;
                    OnPropertyChanged(nameof(CurrentStudent));
                }
            }
        }

        public StudentViewModel()
        {
            CurrentStudent = new Student()
            {
                Name = "李刚的儿",
                Age = 23
            };
        }


        public string this[string columnName]
        {
            get
            {
                if (validator == null)
                {
                    validator = new ViewModelValidator();
                }
                var firstOrDefault = validator.Validate(this)
                    .Errors.FirstOrDefault(lol => lol.PropertyName == columnName);
                return firstOrDefault?.ErrorMessage;
            }
        }
        public string Error
        {
            get
            {
                var results = validator.Validate(this);
                if (results != null && results.Errors.Any())
                {
                    var errors = string.Join(Environment.NewLine, results.Errors.Select(x => x.ErrorMessage).ToArray());
                    return errors;
                }

                return string.Empty;
            }
        }

        private ViewModelValidator validator;
    }
}
```

仔细看上方代码，对比Student.cs，重写自IDataErrorInfo的Error属性定义有所不同。Student.cs对Error基本未做修改，而StudentViewModel.cs有变化，get器中验证属性（简单属性Title和复杂属性CurrentStudent），返回错误提示字符串，诶，CurrentStudent的验证器怎么生效的？有兴趣的大佬可以研究[FluentValidation](https://github.com/JeremySkinner/FluentValidation)库源码一探究竟，我没有深究。

5. StudentViewModel的验证器ViewModelValidator.cs

ViewModel的验证器，相比Student的验证器StudentValidator，就简单的多了，因为只需要编写验证一个简单属性Title的代码。而复杂属性CurrentStudent的验证器StudentValidator，将被WPF属性系统自动调用，即在StudentViewModel的索引器this[string columnName]和Error属性中调用，界面触发规则时自动调用，具体是什么机制我没有仔细研究，有兴趣的大佬可以研究源码。

```C#
using FluentValidation;
using WpfFluentValidation.ViewModels;

namespace WpfFluentValidation.Validators
{
    public class ViewModelValidator:AbstractValidator<StudentViewModel>
    {
        public ViewModelValidator()
        {
            RuleFor(vm => vm.Title)
                .NotEmpty()
                .WithMessage("标题长度不能为空！")
                .Length(5, 30)
                .WithMessage("标题长度限制在5到30个字符之间！");
        }
    }
}
```

6. 辅助类BaseClass.cs

简单封装INotifyPropertyChanged接口

```C#
using System.ComponentModel;

namespace WpfFluentValidation
{
    public class BaseClass : INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler PropertyChanged;
        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChangedEventHandler handler = PropertyChanged;
            if (handler != null)
            {
                handler(this, new PropertyChangedEventArgs(propertyName));
            }
        }
    }
}
```

7. 视图StudentView.xaml

用户直接接触的视图文件来了，比较简单，提供简单属性标题(Title)、复杂属性学生姓名(CurrentStudent.Name)、学生年龄( CurrentStudent .Age)、学生邮政编码( CurrentStudent .Zip)验证，xaml代码如下：

```xml
<UserControl x:Class="WpfFluentValidation.Views.StudentView"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
             xmlns:local="clr-namespace:WpfFluentValidation.Views"
             xmlns:vm="clr-namespace:WpfFluentValidation.ViewModels"
             mc:Ignorable="d" 
             d:DesignHeight="450" d:DesignWidth="800">
    <UserControl.DataContext>
        <vm:StudentViewModel/>
    </UserControl.DataContext>
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <GroupBox Header="ViewModel直接属性验证">
            <StackPanel Orientation="Horizontal">
                <Label Content="标题："/>
                <TextBox Text="{Binding Title, UpdateSourceTrigger=PropertyChanged,ValidatesOnDataErrors=True}"
                         Style="{StaticResource ErrorStyle1}"/>
            </StackPanel>
        </GroupBox>
        <GroupBox Header="ViewModel对象属性CurrentStudent的属性验证" Grid.Row="1">
            <StackPanel>
                <StackPanel Orientation="Horizontal">
                    <Label Content="姓名："/>
                    <TextBox Text="{Binding CurrentStudent.Name, UpdateSourceTrigger=PropertyChanged,ValidatesOnDataErrors=True}"
                         Style="{StaticResource ErrorStyle2}"/>
                </StackPanel>
                <StackPanel Orientation="Horizontal">
                    <Label Content="年龄："/>
                    <TextBox Text="{Binding CurrentStudent.Age, UpdateSourceTrigger=PropertyChanged,ValidatesOnDataErrors=True}"
                         Style="{StaticResource ErrorStyle2}"/>
                </StackPanel>
                <StackPanel Orientation="Horizontal">
                    <Label Content="邮编：" />
                    <TextBox Text="{Binding CurrentStudent.Zip, UpdateSourceTrigger=PropertyChanged,ValidatesOnDataErrors=True}"
                         Style="{StaticResource ErrorStyle2}"/>
                </StackPanel>
            </StackPanel>
        </GroupBox>
    </Grid>
</UserControl>
```

8. 错误提示样式

本文提供了两种样式，具体效果见前面的截图，代码如下：

```xml
<Application x:Class="WpfFluentValidation.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WpfFluentValidation"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <Style TargetType="StackPanel">
            <Setter Property="Margin" Value="0 5"/>
        </Style>
        <!--第一种错误样式，红色边框-->
        <Style TargetType="{x:Type TextBox}" x:Key="ErrorStyle1">
            <Setter Property="Width" Value="200"/>
            <Setter Property="Validation.ErrorTemplate">
                <Setter.Value>
                    <ControlTemplate>
                        <DockPanel>
                            <Grid DockPanel.Dock="Right" Width="16" Height="16"
                            VerticalAlignment="Center" Margin="3 0 0 0">
                                <Ellipse Width="16" Height="16" Fill="Red"/>
                                <Ellipse Width="3" Height="8" 
                                VerticalAlignment="Top" HorizontalAlignment="Center" 
                                Margin="0 2 0 0" Fill="White"/>
                                <Ellipse Width="2" Height="2" VerticalAlignment="Bottom" 
                                HorizontalAlignment="Center" Margin="0 0 0 2" 
                                Fill="White"/>
                            </Grid>
                            <Border BorderBrush="Red" BorderThickness="2" CornerRadius="2">
                                <AdornedElementPlaceholder/>
                            </Border>
                        </DockPanel>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
            <Style.Triggers>
                <Trigger Property="Validation.HasError" Value="true">
                    <Setter Property="ToolTip" Value="{Binding RelativeSource=
                            {x:Static RelativeSource.Self}, 
                            Path=(Validation.Errors)[0].ErrorContent}"/>
                </Trigger>
            </Style.Triggers>
        </Style>

        <!--第二种错误样式，右键文字提示-->
        <Style TargetType="{x:Type TextBox}" x:Key="ErrorStyle2">
            <Setter Property="Width" Value="200"/>
            <Setter Property="Validation.ErrorTemplate">
                <Setter.Value>
                    <ControlTemplate>
                        <StackPanel Orientation="Horizontal">
                            <AdornedElementPlaceholder x:Name="textBox"/>
                            <TextBlock Margin="10" Text="{Binding [0].ErrorContent}" Foreground="Red"/>
                        </StackPanel>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
            <Style.Triggers>
                <Trigger Property="Validation.HasError" Value="true">
                    <Setter Property="ToolTip" Value="{Binding RelativeSource=
                    {x:Static RelativeSource.Self}, 
                    Path=(Validation.Errors)[0].ErrorContent}"/>
                </Trigger>
            </Style.Triggers>
        </Style>
    </Application.Resources>
</Application>
```

## 5. 介绍完毕

码农就是这样，文章基本贴代码，哈哈。

## 6. 源码同步

本文代码已同步：

- gitee: https://gitee.com/dotnet9/FluentValidationForWpf
- github： https://github.com/dotnet9/FluentValidationForWPF