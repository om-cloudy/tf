
resource "aws_vpc" "network" {
  cidr_block = "192.168.0.0/16"

  tags = {
    Name = "tf-vpc"
  }
}

resource "aws_subnet" "pub" {
  vpc_id                  = aws_vpc.network.id
  cidr_block              = "192.168.0.0/20"
  availability_zone       = "ap-south-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "Public-sub"
  }
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.network.id

  tags = {
    Name = "tf-igw"
  }
}

resource "aws_route_table" "rt" {
  vpc_id = aws_vpc.network.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "my-rt"
  }
}

resource "aws_route_table_association" "rs" {
  route_table_id = aws_route_table.rt.id
  subnet_id      = aws_subnet.pub.id
}

resource "aws_security_group" "tf-sg" {
  name   = "my-sg"
  vpc_id = aws_vpc.network.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "my-sg"
  }
}

resource "aws_instance" "vm" {
  ami                         = "ami-0c7632fb77f35db43"
  instance_type               = "t3.micro"
  key_name                    = "omii"
  subnet_id                   = aws_subnet.pub.id
  vpc_security_group_ids      = [aws_security_group.tf-sg.id]

  user_data = <<-EOF
    #!/bin/bash
    apt-get update -y
    apt-get install -y nginx
    systemctl enable --now nginx
    echo '<h1>Hello, This is Terraform</h1>' > /var/www/html/index.html
  EOF

  tags = {
    Name = "tf-instance"
  }
}
