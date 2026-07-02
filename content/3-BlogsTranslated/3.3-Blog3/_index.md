---
title: "Blog 3"
date: 2026-06-20
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Creating 3D Assets from 2D Images Using AI on AWS

The rapid advancement of Generative AI has introduced new possibilities in game development and 3D content creation. Instead of modeling 3D objects manually, developers can now leverage AI models to transform a single 2D concept image into a textured 3D asset within minutes.

In this blog, we explore an AWS-based workflow that combines two open-source AI models—**TripoSG** and **MV-Adapter**—to generate textured 3D models from 2D concept images.

---

# Solution Architecture

The proposed solution is divided into two processing stages to balance GPU performance and operational cost.

**Amazon S3** serves as the central storage service for input images, intermediate files, and the final 3D assets in **GLB** format.

The overall workflow consists of the following steps:

1. Upload a 2D concept image to Amazon S3.
2. Generate the 3D mesh using TripoSG on an Amazon EC2 GPU instance.
3. Store the generated mesh back in Amazon S3.
4. Apply textures using MV-Adapter on another GPU-enabled EC2 instance.
5. Save the completed textured 3D model to Amazon S3.

Separating the workflow into different stages allows each task to run on the most suitable compute resources, improving scalability and reducing unnecessary GPU costs.

---

# Generating the 3D Mesh with TripoSG

The first stage focuses on creating the geometric structure of the 3D model.

GPU-enabled EC2 instances such as the **g4dn** family are well suited for running the TripoSG model, especially when using AWS Deep Learning AMIs that already include CUDA and PyTorch.

The workflow includes:

- Downloading the input image from Amazon S3
- Running the TripoSG model
- Generating the 3D mesh
- Exporting the model in **.glb** format
- Uploading the generated mesh back to Amazon S3

The resulting mesh serves as the foundation for the texturing stage.

---

# Applying Textures with MV-Adapter

Once the mesh has been generated, **MV-Adapter** uses the original 2D image as a reference to generate multi-view textures.

Because this process requires significantly more GPU memory, GPU instances from the **g6e** family are recommended.

A common issue encountered during AI mesh generation is the presence of **non-manifold geometry**, which may interrupt the texture generation process.

Before applying textures, the mesh should be repaired.

Example:

```bash
python fix_manifold.py \
inputs/raw_model.glb \
inputs/manifold_model.glb
```

After repairing the mesh, textures can be generated using:

```bash
python -m scripts.texture_i2tex \
--image inputs/concept.jpeg \
--mesh inputs/manifold_model.glb \
--save_dir outputs \
--remove_bg
```

The output is a textured 3D model that is ready for further optimization.

---

# Optimizing the Generated Model

AI-generated models are generally suitable for prototyping but often require additional optimization before being used in production.

Typical optimization tasks include:

- Reducing polygon count
- Improving topology
- Optimizing UV mapping
- Cleaning mesh geometry
- Refining materials

Applications such as **Blender** provide powerful tools for performing these optimization steps.

---

# Rigging and Animation

After optimization, the next step is adding a skeleton (rigging) so the model can be animated.

Popular options include:

- Using the **Rigify** add-on in Blender to generate a character rig
- Using **Mixamo** to automatically rig characters and apply prebuilt animations

Once rigged, the model can be imported directly into game engines such as Unity or Unreal Engine.

---

# Cost Optimization on AWS

GPU-enabled EC2 instances provide high performance but can become expensive if left running continuously.

Several best practices can help reduce costs:

- Use AWS Deep Learning AMIs with preconfigured CUDA and PyTorch.
- Start GPU instances only when processing is required.
- Store all project data in Amazon S3.
- Stop or terminate EC2 instances immediately after the workflow completes.

Students and beginners can also participate in AWS workshops or AWS Cloud Bootcamps to earn AWS Credits for experimentation and development.

---

# Practical Applications

This workflow can be applied in many scenarios, including:

- Game development
- Character creation
- Metaverse asset generation
- AR/VR applications
- Product visualization
- Rapid prototyping

By combining open-source AI models with AWS GPU infrastructure, developers can significantly reduce the time required to create high-quality 3D assets.

---

# Conclusion

Deploying TripoSG and MV-Adapter on AWS provides an efficient workflow for converting 2D images into textured 3D assets.

Although AI-generated models cannot yet fully replace professional 3D artists, they are highly effective during the prototyping phase, enabling developers to accelerate asset creation while reducing manual modeling effort.

Combined with Amazon EC2 GPU instances and Amazon S3 storage, this solution offers a scalable and flexible workflow suitable for both research projects and real-world game development.