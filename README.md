# Chess Task Data Generator ♟️

A specialized data generator for creating chess mate-in-1 reasoning tasks. Generates chess positions, renders board images, and creates ground truth videos for evaluating video generation models' reasoning capabilities.

Repository: [O_57_chess_data_generator](https://github.com/vm-dataset/O_57_chess_data_generator)

---

## 🚀 Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/vm-dataset/O_57_chess_data_generator.git
cd O_57_chess_data_generator

# 2. Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# 3. Install dependencies
pip install --upgrade pip
pip install -r requirements.txt
pip install -e .

# 4. Generate chess tasks
python examples/generate.py --num-samples 50
```

---

## 📁 Structure

```
chess-task-data-generator/
├── core/                    # ✅ Framework utilities
│   ├── base_generator.py   # Abstract base class
│   ├── schemas.py          # Pydantic models
│   ├── image_utils.py      # Image helpers
│   ├── video_utils.py      # Video generation
│   └── output_writer.py    # File output
├── src/                     # Chess task implementation
│   ├── generator.py        # Chess task generator
│   ├── prompts.py          # Chess prompt templates
│   └── config.py           # Chess configuration
├── examples/
│   └── generate.py         # Entry point
└── data/questions/         # Generated output
```

---

## 📦 Output Format

Every generator produces chess task pairs:

```
data/questions/chess_task/{task_id}/
├── first_frame.png          # Initial chess position (REQUIRED)
├── final_frame.png         # Position after mate move (REQUIRED)
├── prompt.txt              # Instructions (REQUIRED)
└── ground_truth.mp4        # Solution video with piece animation (OPTIONAL)
```

---

## 🎯 Chess Task Features

### Task Types

The generator creates **mate-in-1** chess positions where one side can deliver checkmate in exactly one move.

**Pattern Categories:**
- **Back-rank mates**: King trapped on back rank by own pieces, attacked by rook/queen
- **Queen mates**: Queen + King coordination against cornered enemy king
- **Rook mates**: Rook + King endgame patterns

### Example Position

```
Position (FEN): 6k1/5ppp/8/8/8/8/8/R6K w - - 0 1

Solution: Ra8# (Rook moves from a1 to a8, delivering checkmate)
```

### Visual Features

- **High-quality board rendering**: Uses `chess.svg` + `cairosvg` for crisp board images
- **Smooth piece animation**: Ground truth videos show pieces sliding from start to end position
- **Unicode chess pieces**: Beautiful chess piece symbols (♔♕♖♗♘♙)
- **Standard chess notation**: FEN for positions, UCI for moves

---

## 🎨 Configuration

### Basic Usage

```python
from src import TaskGenerator, TaskConfig
from pathlib import Path

config = TaskConfig(
    num_samples=100,           # Number of tasks to generate
    domain="chess",            # Task domain
    image_size=(512, 512),    # Board image size
    generate_videos=True,      # Generate ground truth videos
    video_fps=10,              # Video frame rate
    random_seed=42             # For reproducibility
)

generator = TaskGenerator(config)
tasks = generator.generate_dataset()
```

### Command Line

```bash
# Generate 50 chess tasks
python examples/generate.py --num-samples 50

# Generate without videos
python examples/generate.py --num-samples 50 --no-videos

# Custom output directory
python examples/generate.py --num-samples 100 --output data/my_chess_tasks

# With seed for reproducibility
python examples/generate.py --num-samples 50 --seed 42
```

---

## 📊 Task Generation Details

### Position Generation

The generator uses the `python-chess` library to:
- Generate valid mate-in-1 positions
- Validate all moves for legality
- Verify checkmate conditions
- Support multiple mate patterns

### Video Generation

When enabled, generates smooth animations showing:
- Initial position (hold frames)
- Piece sliding from start to end square
- Final position (hold frames)

The moving piece maintains full opacity throughout the animation for clarity.

### Prompt System

Prompts are task-type specific:
- **Default**: General mate-in-1 instructions
- **Back-rank**: Specific back-rank mate descriptions
- **Queen/Rook**: Pattern-specific instructions

---

## 🔧 Dependencies

### Required
- `python-chess` - Chess logic and validation
- `Pillow` - Image rendering
- `pydantic` - Configuration management
- `opencv-python` - Video generation

### Optional
- `cairosvg` - High-quality SVG to PNG conversion (recommended)

---

## 📝 Example Output

Each generated task includes:

1. **first_frame.png**: Initial chess position showing the mate-in-1 setup
2. **final_frame.png**: Position after the winning move is played
3. **prompt.txt**: Natural language instruction for the video model
4. **ground_truth.mp4**: (Optional) Animated solution showing piece movement

### Metadata

Task metadata includes:
- FEN notation of initial position
- UCI notation of the mate move
- Task type (back_rank, queen_mate, rook_mate)
- Difficulty level

---

## 🎓 Use Cases

This generator is designed for:

- **Video Model Evaluation**: Test video generation models' ability to understand chess and demonstrate solutions
- **Reasoning Benchmarks**: Create datasets for evaluating spatial reasoning and strategic thinking
- **Research**: Study how models handle chess positions and tactical patterns
- **VMEvalKit Integration**: Compatible with VMEvalKit's evaluation framework

---

## 🔗 Related Projects

- [VMEvalKit](https://github.com/Video-Reason/VMEvalKit) - Framework for evaluating reasoning in video models
- [python-chess](https://github.com/niklasf/python-chess) - Chess library used for position generation

---

## 📄 License

MIT License - see LICENSE file for details

---

## 🤝 Contributing

Contributions welcome! Areas for improvement:
- Additional mate patterns (knight mates, discovered attacks, etc.)
- More diverse position generation
- Enhanced video animation effects
- Integration with chess engines for dynamic generation
